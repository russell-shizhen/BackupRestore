# A step by step tutorial to test Android backup and restore

## Environment

- Android 7.0+
- Google Play is present on device

## Prepare device

1. Add your Google Account, e.g. `shizhen.sg@gmail.com` to your device.
2. Select the Google Account `shizhen.sg@gmail.com` as backup account inside device backup settings. Usually, it is under **Settings** -> **System** -> **Backup** -> **Account**.
3. Remember to turn on Back up to Google Drive. See below screenshot

    ![Backup Settings on Device](https://raw.githubusercontent.com/russell-shizhen/BackupRestore/master/images/Screenshot%20(Sep%2012%2C%202019%202_07_09%20PM).png)

## Turn on Android app `allowBackup` attribute

- Set `android:allowBackup="true"` inside `AndroidManifest.xml`.

## Test flows

1. Install ![BackupRestore.apk]().
2. Open the app and modify the two `EditText` field, click on button SAVE. E.g.

   ![Screenshot](https://raw.githubusercontent.com/russell-shizhen/BackupRestore/master/images/Screenshot%20(Sep%2012%2C%202019%202_07_09%20PM).png).

3. Verify that the shared preferance already saved correctly. E.g.

   ![Screenshot](https://raw.githubusercontent.com/russell-shizhen/BackupRestore/master/images/Screenshot%202019-09-12%20at%202.45.38%20PM.png).

4. Perfom a backup.

    Check availble transport

    ```bash
    $adb shell bmgr list transports

      android/com.android.internal.backup.LocalTransport
      com.google.android.gms/.backup.migrate.service.D2dTransport
    * com.google.android.gms/.backup.BackupTransportService
    ```

    ```bash
    #On Android 7.0 or later
    $adb shell bmgr backupnow com.arophix.backup.restore
    ```

    You should see something like below:

    ```bash
    Running incremental backup for 1 requested packages.
    Package @pm@ with result: Success
    Package com.arophix.backup.restore with progress: 512/1024
    Package com.arophix.backup.restore with progress: 1536/1024
    Package com.arophix.backup.restore with progress: 2560/1024
    Package com.arophix.backup.restore with result: Success
    Backup finished with result: Success
    ```

5. Perfom a restore.

    Clear the app storage first.

    ![Screenshot](https://raw.githubusercontent.com/russell-shizhen/BackupRestore/master/images/Screenshot%20(Sep%2012%2C%202019%202_47_20%20PM).png).

    Check the token name of the dataset to retore using below command

    ```bash
    $adb shell dumpsys backup | grep Current
    ```

    You should see something like below, the hex string after `Current` is the token name to use.

    ```bash
    Current transport clients: 0
    Current:   30ebe10fc40cee0a
    ```

    Restore date using below command

    ```bash
    $adb shell bmgr restore 30ebe10fc40cee0a  com.arophix.backup.restore
    ```

    You should see something like below

    ```bash
    Scheduling restore: Pixel 2
    restoreStarting: 1 packages
    onUpdate: 0 = com.arophix.backup.restore
    restoreFinished: 0
    done
    ```

6. Verify the data is restored

    Open the app and click on button `REFRESH`. You should see the two `EditText` fields are updated to the content restored from GoogleDrive.

## Reference

- [Testing Android Backup and Restore](https://developer.android.com/guide/topics/data/testingbackup)
