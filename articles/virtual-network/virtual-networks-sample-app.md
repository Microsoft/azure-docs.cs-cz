---
title: Azure ukázkovou aplikaci pro použití s zóny DMZ
titlesuffix: Azure Virtual Network
description: Nasazení této jednoduché webové aplikace po vytvoření DMZ do testování scénářů, tok provozu
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: a8e52af1a1feb8a01ed5556efb6e153c56b25cca
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700583"
---
# <a name="sample-application-for-use-with-dmzs"></a>Ukázková aplikace pro použití s zóny DMZ
[Vraťte se na stránku osvědčené postupy zabezpečení hranic][HOME]

Tyto skripty prostředí PowerShell můžete spustit místně na serverech IIS01 a AppVM01 instalace a nastavení, která se zobrazí stránka html z front-endový server IIS01 s obsahem z back endového serveru AppVM01 jednoduchou webovou aplikaci.

Tato aplikace poskytuje jednoduché testovacím prostředí pro řadu příkladů DMZ a jak změny na koncové body, skupiny zabezpečení sítě, směrování definovaného uživatelem a brány Firewall pravidla mohou ovlivnit přenosové toky.

## <a name="firewall-rule-to-allow-icmp"></a>Pravidlo brány firewall umožňující ICMP
Tento jednoduchý příkaz prostředí PowerShell můžete spustit na virtuálních počítačích Windows povolit provoz protokolu ICMP (Ping). Tato aktualizace brány firewall umožňuje pro snazší testování a řešení potíží s tím, že protokol ping přes bránu firewall systému windows (na většině distribucí Linuxu, které ICMP je ve výchozím).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Pokud používáte tyto skripty, je toto přidání pravidla brány firewall na první příkaz.

## <a name="iis01---web-application-installation-script"></a>IIS01 - instalační skript pro webové aplikace
Tento skript se:

1. Otevřete IMCPv4 (Ping) na místním serveru brány windows firewall pro snazší testování
2. Instalace IIS a rozhraní .net Framework v4.5
3. Vytvořit webovou stránku ASP.NET a v souboru Web.config
4. Změnit výchozí fond aplikací pro usnadnění přístupu k souborům
5. Nastavit pro anonymní uživatele pro účet správce a heslo

Tento skript Powershellu by měl spustit místně, zatímco RDP měl do IIS01.

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesn''t really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - soubor skriptu instalace serveru
Tento skript vytvoří back endu pro tuto jednoduchou aplikaci. Tento skript se:

1. Otevřete IMCPv4 (Ping) v bráně firewall pro snazší testování
2. Vytvořte adresář pro webové stránky
3. Vytvořte textový soubor, který se vzdáleně přístupný web webové stránky
4. Nastavení oprávnění pro adresáře a souboru pro anonymní přístup
5. Vypněte rozšířené zabezpečení Internet Exploreru umožňuje snadnější prohlížení z tohoto serveru 

> [!IMPORTANT]
> **Osvědčený postup**: Nikdy vypněte rozšířené zabezpečení Internet Exploreru na provozním serveru. Navíc je obecně vhodné k procházení webu v provozním serveru. Navíc se vám otevírají sdílené složky pro anonymní přístup není dobrý nápad, ale Hotovo zde pro zjednodušení.
> 
> 

Tento skript Powershellu by měl spustit místně, zatímco RDP měl do AppVM01. PowerShell je potřeba spustit jako správce k zajištění úspěšného provedení.

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - skriptu pro instalaci serveru DNS
Neexistuje žádný skript zahrnuté v této ukázkové aplikaci k nastavení serveru DNS. Pokud testování pravidel brány firewall, skupiny zabezpečení sítě nebo UDR musí obsahovat provoz DNS, DNS01 server musí být nastaveny ručně. Soubor xml konfigurace sítě a šablony Resource Manageru pro oba příklady zahrnuje DNS01 jako primární server DNS a veřejný server DNS hostitelem Level 3 jako záložní server DNS. Server DNS úroveň 3 by být skutečný server DNS použít pro jiné než místní provoz a s DNS01 není nastaven, v místní síti, který by tomu bylo DNS.

## <a name="next-steps"></a>Další postup
* Spusťte skript IIS01 na serveru služby IIS
* Spusťte skript souborového serveru na AppVM01
* Přejděte na veřejnou IP adresu na IIS01 ověření sestavení

<!--Link References-->
[HOME]: ../best-practices-network-security.md
