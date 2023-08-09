A pesar de que Windows PowerShell ha estado alrededor por un tiempo, hay un montón de administradores que pueden no aventurarse voluntariamente en la línea de comandos, familiarizados con lo que es un cmdlet PowerShell. Pero, a medida que Microsoft amplía la funcionalidad de PowerShell, los administradores deben interesarse por entender los fundamentos de sus capacidades.

Echemos un vistazo a 25 comandos básicos [de PowerShell](https://searchdatacenter.techtarget.com/es/cronica/Microsoft-ajusta-Windows-PowerShell-DSC-en-Windows-Server-2016?_gl=1*13s304u*_ga*OTcwNTI3NDIxLjE2OTE0Mzk3MjQ.*_ga_TQKE4GS5P9*MTY5MTQ1NDgyOC4zLjAuMTY5MTQ1NDgyOC4wLjAuMA..&_ga=2.167397246.1589857046.1691439724-970527421.1691439724) con los que puede ejecutar tareas. No solo son comunes las tareas mismas, sino que las estructuras de los comandos muestran la sintaxis y la estructura de otros comandos de PowerShell. Estos comandos básicos de PowerShell deben hacer que usted comience el camino para convertirse en un maestro.

### **Entradas communes (solo para empezar)**

1. Navegue el Windows Registry como el sistema de archivos:  
cd hkcu:

2. Busque recursivamente una determinada cadena dentro de los archivos:  
dir –r | select string "searchforthis"

3. Encuentre los cinco procesos que usan más memoria:  
ps | sort –p ws | select –last 5

4. Haga un ciclo de un servicio (detenerlo, y luego reiniciarlo) como el protocolo de configuración de huésped dinámico (DHCP):  
Restart-Service DHCP

5. Liste todos los elementos dentro de una carpeta:  
Get-ChildItem – Force

6. Recurra sobre una serie de directorios o carpetas:  
Get-ChildItem –Force c:\directory –Recurse

7. Elimine todos los archivos dentro de un directorio sin que se le pida entrar a cada uno de ellos:  
Remove-Item C:\tobedeleted –Recurse

8. Reinicie la computadora actual:  
(Get-WmiObject -Class Win32_OperatingSystem -ComputerName .).Win32Shutdown(2)

### **Recolectar información**

9. Obtenga información sobre la fabricación y modelo de una computadora:  
Get-WmiObject -Class Win32_ComputerSystem

10. Obtenga información sobre la BIOS de la computadora actual:  
Get-WmiObject -Class Win32_BIOS -ComputerName .

11. Lista de arreglos en caliente instalados: actualización rápidas de ingeniería (QFE) o archivos de Windows Update:  
Get-WmiObject -Class Win32_QuickFixEngineering -ComputerName .

12. Obtenga el nombre de usuario de la persona actualmente registrada en una computadora:  
Get-WmiObject -Class Win32_ComputerSystem -Property UserName -ComputerName .

13. Encuentre solo los nombres de las aplicaciones instaladas en la computadora actual:  
Get-WmiObject -Class Win32_Product -ComputerName . | Format-Wide -Column 1

14. Obtenga direcciones IP asignadas a la computadora actual:  
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Format-Table -Property IPAddress

15. Obtenga un reporte de configuración IP más detallada para la máquina actual:  
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Select-Object -Property [a-z]* -ExcludeProperty IPX*,WINS*

16. Encuentre las tarjetas de red con DHCP habilitado en la computadora actual:  
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "DHCPEnabled=true" -ComputerName .

17. Habilite DHCP en todos los adaptadores de red en la computadora actual:  
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=true -ComputerName . | ForEach-Object -Process {$_.EnableDHCP()}

### **Gestión de software**

18. Instale un paquete MSI en una computadora remota:  
(Get-WMIObject -ComputerName TARGETMACHINE -List | Where-Object -FilterScript {$_.Name -eq "Win32_Product"}).Install(\\MACHINEWHEREMSIRESIDES\path\package.msi)

19. Actualice una aplicación instalada con un paquete de actualización de aplicaciones basado en MSI:  
(Get-WmiObject -Class Win32_Product -ComputerName . -Filter "Name='name_of_app_to_be_upgraded'").Upgrade(\\MACHINEWHEREMSIRESIDES\path\upgrade_package.msi)

20. Elimine un paquete MSI de la computadora actual:  
(Get-WmiObject -Class Win32_Product -Filter "Name='product_to_remove'" -ComputerName . ).Uninstall()

### **Gestión de la máquina**

21. Remotamente apague otra máquina después de un minuto:  
Start-Sleep 60; Restart-Computer –Force –ComputerName TARGETMACHINE

22. Añada una impresora:  
(New-Object -ComObject WScript.Network).AddWindowsPrinterConnection("\\printerserver\hplaser3")

23. Elimine una impresora:  
(New-Object -ComObject WScript.Network).RemovePrinterConnection("\\printerserver\hplaser3 ")

24. Entre en una sesión remota de PowerShell (debe tener habilitada la gestión remota):  
enter-pssession TARGETMACHINE

25. Use el commando de invocación de PowerShell para ejecutar un script en un servidor remoto:  
invoke-command -computername machine1, machine2 -filepath c:\Script\script.ps1

### **Comando adicional**

Para desechar un proceso, puede utilizar el ID del proceso o el nombre del proceso. El modificador -processname permite el uso de comodines. Aquí está cómo detener la calculadora:  
Stop-Process -processname calc*