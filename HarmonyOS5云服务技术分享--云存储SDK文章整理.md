### Guide to Integrating Huawei Cloud Storage SDK in HarmonyOS ArkTS Apps  

Hello everyone! Today, we'll discuss how to integrate the Huawei Cloud Storage SDK into HarmonyOS apps based on ArkTS (API 9-11). This guide will walk you through the complete process from environment setup to code implementation, with tips for common issues along the way~  


### I. Preparation Essentials  
#### 1.1 Development Environment Requirements  
- **Development Tool**: HUAWEI DevEco Studio 3.1+ (recommend using the latest version)  
- **SDK Versions**:  
  - Compile SDK Version ≥ 9  
  - Compatible SDK Version ≥ 9  
- **Project Structure**: Must use Stage model (check the box when creating a new project)  

📢 **Tip**: For migration from legacy projects, confirm `apiType` is `stageMode` in `build-profile.json5`.  

#### 1.2 Obtain Configuration Files  
1. Log in to the Huawei Developer Alliance console.  
2. Go to **Project Settings > Cloud Development > Cloud Storage**.  
3. Download the `agconnect-services.json` file.  
4. Check for the following key configurations:  
   ```json  
   "cloudstorage": {  
       "default_storage": "Your default bucket name",  
       "storage_url": "https://agc-storage-drcn.platform.dbankcloud.cn"  
   }  
   ```  
   ⚠️ **Important**: Initialization fails if `default_storage` is unconfigured!  


### II. Full Project Configuration Process  
#### 2.1 Place Configuration File  
- Create a directory in the project: `entry/src/main/resources/rawfile`  
- Drag the downloaded `agconnect-services.json` into this directory.  

#### 2.2 Add SDK Dependencies  
1. Open `oh-package.json5` and add:  
   ```json  
   "dependencies": {  
       "@hw-agconnect/hmcore": "^1.0.1",  
       "@hw-agconnect/cloud": "^1.0.1"  
   }  
   ```  
2. Sync via command line (or click "Sync Now" in IDE):  
   ```bash  
   cd entry  
   ohpm install  
   ```  


### III. Step-by-Step Code Implementation  
#### 3.1 SDK Initialization  
Add to `EntryAbility.ets`:  
```typescript  
import { initialize } from '@hw-agconnect/hmcore';  
import util from '@ohos/util';  

// Initialize in the onCreate lifecycle  
async onCreate() {  
    // Read configuration file  
    const input = await this.context.resourceManager.getRawFileContent('agconnect-services.json');  
    const jsonString = new util.TextDecoder().decodeWithStream(input);  
    
    // Perform initialization  
    initialize(this.context, JSON.parse(jsonString));  
}  
```  

#### 3.2 Add Network Permissions  
Add to `module.json5`:  
```json  
"requestPermissions": [  
    {  
        "name": "ohos.permission.INTERNET"  
    }  
]  
```  
🔐 If accessing local storage, dynamically request permissions like `ohos.permission.READ_MEDIA`.  


### IV. Advanced Configuration (Optional)  
#### 4.1 Manual Key Configuration  
If the config file lacks keys, supplement via code:  
```typescript  
import { setApiKey, setClientSecret } from '@hw-agconnect/hmcore';  

// Add after initialize  
setApiKey("Your API key");  
setClientSecret("Your client secret");  
```  

#### 4.2 Multi-Storage Instance Configuration  
After creating multiple buckets in the cloud console, access by specifying the instance name:  
```typescript  
const storage = cloud.storage("Your other storage instance name");  
```  


### V. Quick Verification Tips  
After integration, test file upload:  
```typescript  
// Get the default storage instance  
const storage = cloud.storage();  

// Create a file reference  
const fileRef = storage.ref('test/hello.txt');  

// Upload text content  
fileRef.putString('Hello HarmonyOS!').then(() => {  
    console.log('Upload successful!');  
});  
```  
Check if `test/hello.txt` appears in the cloud storage management console~  


### VI. Common Issues Troubleshooting  
#### Initialization Failure  
- Verify the `agconnect-services.json` path is correct.  
- Ensure `default_storage` matches the console configuration.  
- Check for missing network permissions.  

#### Dependency Conflicts  
- Try `ohpm update` to refresh dependency versions.  
- Check version numbers in `oh-package-lock.json`.  

#### Real Device Debugging Issues  
- Ensure the device is logged in to a Huawei developer account.  
- Verify that the signature certificate matches console configurations.  


Hope this guide helps you seamlessly integrate Huawei Cloud Storage! If you encounter any issues, feel free to share them in the comments. Your integration experiences are welcome as we build a stronger HarmonyOS ecosystem together~  

Don’t forget to leave a like ❤️—your support motivates us to create more quality tutorials! See you next time~
