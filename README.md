# README

## Create C# Wep-API Project

```sh
dotnet new webapi -o api
```

- แก้ไขชื่อ profile ใน launchSettings.json จาก api ไปเป็น Dev
- แก้ไข applicationUrl ใน Dev profile

  ```json
  {
    ...
    "applicationUrl": "https://localhost:7299;http://localhost:5155",
  }
  ```

  > ไปเป็น

  ```json
  {
    ...
    "applicationUrl": "http://localhost:5037",
  }
  ```

  - Run app

  ```shell
  dotnet run
  ```

  > หรือ

  ```shell
  dotnet run --launch-profile Dev
  ```

## ความสัมพันธ์ระหว่าง `launchSettings.json`, `appsettings.json` และ `appsettings.Development.json`

- launchSettings.json

  ```json
  // launchSettings.json
  {
    "$schema": "https://json.schemastore.org/launchsettings.json",
    "iisSettings": {
      ...
    },
    "profiles": {
      "Dev": {
        ...
        "environmentVariables": {
          "ASPNETCORE_ENVIRONMENT": "Development"
        }
      },
      "IIS Express": {
        ...
        "environmentVariables": {
          "ASPNETCORE_ENVIRONMENT": "Development"
        }
      }
    }
  }
  ```

  > ถ้าไม่ระบุ profile ตัว Launcher จะเอา profile ตัวแรกมารัน
  > ถ้าระบุ profile ตัว Launcher จะเอา profile ที่ตรงกับค่า profile ที่ได้รับมารัน

- appsettings.json และ appsettings.Development.json

  ```json
  {
    "Logging": {
      "LogLevel": {
        "Default": "Information",
        "Microsoft.AspNetCore": "Warning"
      }
    },
    "AllowedHosts": "*"
  }
  ```

  > จากตัวอย่างข้างต้น คือผลลัพธ์หลังจากอ่านทั้ง 2 ไฟลล์เรียบร้อยแล้ว ตัว appsettings.json จะถูกอ่านขึ้นมาก่อนหลังจากนั้นจะอ่าน appsettings.Development.json ตาม profile ที่ใช้ run แล้วเมื่อเจอค่าที่ตรงกัน ค่าใน appsettings.Development.json จะไปแทนที่ค่าเดิมที่อ่านมาจาก appsettings.json

## Add new profile `Staging`

- เพิ่ม profile `Staging` เข้าไปใน launchSettings.json

  ```json
  {
    "$schema": "https://json.schemastore.org/launchsettings.json",
    "iisSettings": {
      ...
    },
    "profiles": {
      "Dev": {
        ...
        "environmentVariables": {
          "ASPNETCORE_ENVIRONMENT": "Development"
        }
      },
      "Staging": {
        "commandName": "Project",
        "dotnetRunMessages": true,
        "launchBrowser": true,
        "launchUrl": "swagger",
        "applicationUrl": "http://localhost:5038",
        "environmentVariables": {
          "ASPNETCORE_ENVIRONMENT": "Staging"
        }
      },
      "IIS Express": {
        ...
        "environmentVariables": {
          "ASPNETCORE_ENVIRONMENT": "Development"
        }
      }
    }
  }
  ```

- สร้าง appsettings.Staging.json โดย copy จาก appsettings.Development.json

  ```json
  {
    "Logging": {
      "LogLevel": {
        "Default": "Information",
        "Microsoft.AspNetCore": "Warning"
      }
    }
  }
  ```

- Run App with `Dev`

  ```shell
  dotnet run
  ```

  > หรือ

  ```shell
  dotnet run --launch-profile Dev
  ```

  ```text
    Building...
  info: Microsoft.Hosting.Lifetime[14]
        Now listening on: http://localhost:5037
  info: Microsoft.Hosting.Lifetime[0]
        Application started. Press Ctrl+C to shut down.
  info: Microsoft.Hosting.Lifetime[0]
        Hosting environment: Development
  info: Microsoft.Hosting.Lifetime[0]
        Content root path: /<path to>/api/
  ```

  - ทดสอบด้วย เปิด web browser ด้วย url `http://localhost:5037/WeatherForecast`
  - ทดสอบด้วย เปิด web browser ด้วย url `http://localhost:5037/swagger/index.html`

- Run App with `Staging`

  ```shell
  dotnet run --launch-profile Staging
  ```

  ```text
    Building...
  info: Microsoft.Hosting.Lifetime[14]
        Now listening on: http://localhost:5038
  info: Microsoft.Hosting.Lifetime[0]
        Application started. Press Ctrl+C to shut down.
  info: Microsoft.Hosting.Lifetime[0]
        Hosting environment: Staging
  info: Microsoft.Hosting.Lifetime[0]
        Content root path: /<path to>/api/
  ```

  - ทดสอบด้วย เปิด web browser ด้วย url `http://localhost:5038/WeatherForecast`
  - ทดสอบด้วย เปิด web browser ด้วย url `http://localhost:5038/swagger/index.html` (เปิดไม่ขึ้น) เนื่องจากใน Program.cs ระปุว่าจะให้เรียก `swagger` ได้เมื่อใช้ Dev profile

    ```csharp
    if (app.Environment.IsDevelopment())
    {
        app.UseSwagger();
        app.UseSwaggerUI();
    }
    ```

## Appsettings Variables

- เพิ่มตัวแปร "UseSwagger": false

  - appsettings.json

    ```json
    {
      ...
      "AllowedHosts": "*",
      "UseSwagger": false
    }
    ```

  - appsettings.Development.json

    ```json
    {
      ...
      "AllowedHosts": "*",
      "UseSwagger": true
    }
    ```

  - appsettings.Staging.json

    ```json
    {
      ...
      "AllowedHosts": "*",
      "UseSwagger": true
    }
    ```

  - เปลี่ยนจากใช้ `app.Environment.IsDevelopment()` ไปเป็น `app.Configuration.GetValue<bool>("UseSwagger")` ใน Program.cs

    ```csharp
    if (app.Environment.IsDevelopment())
    {
        app.UseSwagger();
        app.UseSwaggerUI();
    }
    ```

    > ไปเป็น

    ```csharp
    if (app.Configuration.GetValue<bool>("UseSwagger"))
    {
        app.UseSwagger();
        app.UseSwaggerUI();
    }
    ```

  - ทดสอบด้วย เปิด web browser ด้วย url `http://localhost:5037/swagger/index.html`
  - ทดสอบด้วย เปิด web browser ด้วย url `http://localhost:5038/swagger/index.html`

- exercise ลองแก้ให้ Staging ไม่รัน swagger
