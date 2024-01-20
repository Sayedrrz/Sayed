
// Import required libraries
import android.app.Activity;
import android.content.Intent;
import android.hardware.display.DisplayManager;
import android.hardware.display.VirtualDisplay;
import android.media.MediaRecorder;
import android.media.projection.MediaProjection;
import android.media.projection.MediaProjectionManager;
import android.os.Bundle;
import android.util.DisplayMetrics;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

// Create a class for the main activity
public class MainActivity extends Activity {

  // Define the required variables
  private static final String TAG = "MainActivity";
  private static final int REQUEST_CODE = 1000;

  private int mScreenDensity;
  private MediaProjectionManager mProjectionManager;
  private MediaProjection mMediaProjection;
  private VirtualDisplay mVirtualDisplay;
  private MediaProjectionCallback mMediaProjectionCallback;
  private MediaRecorder mMediaRecorder;

  private Button mButtonToggle;
  private boolean mScreenSharing;

  // Create the activity and initialize the elements
  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // Get the screen density
    DisplayMetrics metrics = new DisplayMetrics();
    getWindowManager().getDefaultDisplay().getMetrics(metrics);
    mScreenDensity = metrics.densityDpi;

    // Get the media projection manager
    mProjectionManager = (MediaProjectionManager) getSystemService
        (android.content.Context.MEDIA_PROJECTION_SERVICE);

    // Get the toggle button and set a listener for the click
    mButtonToggle = (Button) findViewById(R.id.toggle);
    mButtonToggle.setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View v) {
        // Toggle the screen sharing state
        if (mScreenSharing) {
          stopScreenSharing();
        } else {
          startScreenSharing();
        }
      }
    });

    // Create a MediaRecorder object
    mMediaRecorder = new MediaRecorder();

    // Create a MediaProjectionCallback object
    mMediaProjectionCallback = new MediaProjectionCallback();
  }

  // Request permission for media projection
  private void startScreenSharing() {
    // Create an intent for media projection
    Intent intent = mProjectionManager.createScreenCaptureIntent();
    // Start the activity to get the result
    startActivityForResult(intent, REQUEST_CODE);
  }

  // Stop screen sharing
  private void stopScreenSharing() {
    // If there is a virtual display, stop it and release it
    if (mVirtualDisplay == null) {
      return;
    }
    mVirtualDisplay.release();
    mVirtualDisplay = null;
    // Stop the MediaRecorder and reset it
    mMediaRecorder.stop();
    mMediaRecorder.reset();
    // Stop the media projection and unregister the callback
    if (mMediaProjection != null) {
      mMediaProjection.stop();
      mMediaProjection.unregisterCallback(mMediaProjectionCallback);
    }
    // Change the screen sharing state and the button text
    mScreenSharing = false;
    mButtonToggle.setText(R.string.start);
  }

  // Receive the result from the previous activity
  @Override# Sayed
