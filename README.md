# LocalFlow.AppHost

Aplicación anfitriona de .NET Aspire para ejecutar y coordinar los proyectos de LocalFlow.

## Requisitos

- Windows, macOS o Linux.
- Git.
- .NET SDK 10.0.401 o una versión posterior compatible con `global.json`.
- Visual Studio 2026 con las herramientas de .NET y .NET Aspire, o el SDK de .NET instalado para trabajar desde terminal.

Comprueba la instalación con:

```powershell
dotnet --version
git --version
```

## Clonar en otro equipo

```powershell
git clone https://github.com/LuigiLlerenaP/LocalFlow.AppHost.git
Set-Location .\LocalFlow.AppHost
dotnet restore .\LocalFlow.AppHost.slnx
dotnet build .\LocalFlow.AppHost.slnx
```

Si el repositorio es privado, autentícate en GitHub con Git Credential Manager o con el método de autenticación configurado para tu cuenta.

## Ejecutar el AppHost

Desde la raíz del repositorio:

```powershell
dotnet run --project .\LocalFlow.AppHost\LocalFlow.AppHost.csproj
```

También puedes abrir `LocalFlow.AppHost.slnx` en Visual Studio y ejecutar el proyecto `LocalFlow.AppHost`.

Las URLs y puertos de desarrollo están definidos en `LocalFlow.AppHost/Properties/launchSettings.json`. No dependas de esos puertos para producción: pueden cambiar si ya están ocupados en otro equipo.

## Agregar proyectos

1. Copia o clona cada proyecto dentro de una carpeta del repositorio, por ejemplo `src\LocalFlow.Api`.
2. Agrega el proyecto a la solución:

   ```powershell
   dotnet sln .\LocalFlow.AppHost.slnx add .\src\LocalFlow.Api\LocalFlow.Api.csproj
   ```

3. Agrega la referencia del proyecto al AppHost:

   ```powershell
   dotnet add .\LocalFlow.AppHost\LocalFlow.AppHost.csproj reference .\src\LocalFlow.Api\LocalFlow.Api.csproj
   ```

4. Registra el proyecto en `LocalFlow.AppHost/AppHost.cs` usando el tipo generado por el proyecto:

   ```csharp
   var builder = DistributedApplication.CreateBuilder(args);

   var api = builder.AddProject<Projects.LocalFlow_Api>("api");

   builder.Build().Run();
   ```

   El nombre del tipo puede variar según el nombre del proyecto; Visual Studio o el compilador indicarán el nombre correcto.
5. Ejecuta `dotnet restore`, `dotnet build` y luego el AppHost para comprobar la integración.
6. Revisa los cambios antes de confirmarlos:

   ```powershell
   git status
   git diff
   ```

No agregues carpetas `bin`, `obj`, `.vs`, archivos de usuario ni secretos al repositorio.

## Configuración y secretos

La configuración común puede versionarse en `appsettings.json`. Los secretos y valores específicos de cada equipo deben mantenerse fuera de Git, por ejemplo usando User Secrets:

```powershell
dotnet user-secrets --project .\LocalFlow.AppHost\LocalFlow.AppHost.csproj set "ConnectionStrings:Default" "valor-local"
```

Cada desarrollador debe configurar sus propios secretos después de clonar. Nunca incluyas contraseñas, tokens, cadenas de conexión reales o certificados privados en `appsettings*.json`.

## Flujo de trabajo Git

```powershell
git pull origin master
git status
git add .
git commit -m "Describe el cambio"
git push origin master
```

Antes de hacer `push`, confirma que `git status` no muestra archivos locales o secretos.
