# Itiner Workspace Exchange Appointment Add-on Installation Guide

## 1. Preparation

### 1.1 System Requirements
- Windows operating system
- Internet Information Services (IIS)
- HTTPS certificate 
- Installed Itiner Workspace application
- A **working Exchange Server** configured and accessible (e.g., Exchange Web Services).

### 1.2 File System
The Exchange Appointment Add-on **must be located in the same folder as the Itiner Workspace application**.  
Recommended structure:  
`D:\ItinerWorkspace\ExchangeAppointment\`

---

## 2. Installation Steps

### 2.1 Copy the Exchange Appointment Add-on Folder
1. Unzip the `ItinerWorkspaceService_ExchangeAppointment_exchangeappointment-x.x.x.ZIP`.
2. Copy the `ExchangeAppointment` folder to `D:\ItinerWorkspace\`.

---

## 3. Exchange Appointment Add-on Setup

### 3.1 Required Files
Ensure the following files exist in the `ExchangeAppointment` folder:
- `runtimes` folder
- `.dll` files
- `appsettings.json`
- `ProtocolExchangeAppointmentWebhook` file
- `.deps` files
- `appsettings.json`
- `Web.config`

### 3.2 IIS Application Pool
1. Open IIS Manager.
2. Create a new application pool:
   - **Name**: `ItinerWorkspace_ExchangeAppointment` (or custom)
   - **.NET CLR Version**: No Managed Code
   - **Managed Pipeline Mode**: Integrated

### 3.3 Add IIS Web Application - Website Configuration
Install the Exchange Appointment Add-on on the same site as Itiner Workspace (e.g., `Default Website`).

1. Open IIS Manager.
2. Add a new web application with the following settings:
   - **Alias**: `ExchangeAppointment`
   - **Application Pool**: Select the previously created pool (e.g., `ItinerWorkspace_ExchangeAppointment`).
   - **Physical Path**: `D:\ItinerWorkspace\ExchangeAppointment`


### 3.4 Configuration
Edit the `appsettings.json` file with the following parameters:
> 💡 For reference, see the `exchangeappointment_appsettings_sample.json` file located in the GitHub repository.


#### Host Configuration
```json
"Host": {
  "WebHostUrl": "https://addonhost/workspace/exchangeappointment",
  "WSUrl": "http://workspacehost/workspace/api",
  "WsApiKey": "test", // API key generated for integration user
  "HealthCheckBaseUrl": "http://addonhost", // optional
  "CustomApiKey": "test",
  "PathBase": "/workspace/exchangeappointment",
  "DebugMode": false,
  "DisableRequestLog": false,
  "DisableMetadata": true,
  "VariablePrefix": "" // Ensures compatibility when the workflow sending events to the integration service is an embedded workflow and uses prefixed variable names.
},
"Storage": {
        "Password": "test" // This password has to match with the storage.password value in the Workspace appsettings.json
    },
```

#### Exchange Server Settings
```json
"Exchange": {
  "Host": "https://mail.domain.com/EWS/Exchange.asmx",
  "Username": "your-username",
  "Password": "your-password",
  "Domain": "your-domain"
},
```

#### HMAC Configuration
```json
"Hmac": {
  "Secret": "demo"
}
```

#### Reference Filter Configuration
- **Purpose**: Controls which events the integration service processes based on the `Reference` value in the event.
- **Behavior**:
  - If the `Filter` list is **not empty**, the integration service will **only process events** that contain a reference included in the `Filter` list.
  - If the `Filter` list is **empty**, the service will process **all events**, regardless of their `Reference` value.
- **Usage**: Populate the `Filter` list with user-defined keys to restrict the scope of processed events.

```json
"Reference": {
  "Filter": ["Email"]
},
```

#### Logging (Serilog) Configuration
```json
"Serilog": {
  "Using": ["Serilog.Sinks.Seq"],
  "LevelSwitches": {
    "$controlSwitch": "Information"
  },
  "MinimumLevel": {
    "ControlledBy": "$controlSwitch",
    "Override": {
      "System": "Warning",
      "Microsoft": "Error"
    }
  },
  "WriteTo": [{
    "Name": "Seq",
    "Args": {
      "serverUrl": "http://global_seq:5341",
      "apiKey": ""
    }
  }],
  "Enrich": ["FromLogContext", "WithMachineName", "WithThreadId"],
  "Properties": {
    "Application": "EmailWorker"
  }
}
```

---

## 4. Logging
By default, logs are sent to an SEQ server. Optionally, configure file or database logging as needed.

---

## 5. Restart
After completing the setup, restart the application pool in IIS Manager to apply the changes.

---

## 6. Updating the Exchange Appointment Add-on
1. **Stop** the application pool in IIS Manager.
2. Replace the existing `ExchangeAppointment` folder with the updated version.
3. If required, modify the `appsettings.json` file and add any new keys provided.
4. **Restart** the application pool to activate the updated configuration.

---

This guide details the installation and update process for the Exchange Appointment Add-on within the Itiner Workspace application. For additional support, consult your development team or system administrator.
