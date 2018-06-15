---
title: Azure ukázkovou aplikaci pro použití s zóny DMZ | Microsoft Docs
description: Nasazení této jednoduché webové aplikace po vytvoření DMZ k otestování scénářů tok provozu
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: 60340ab7-b82b-40e0-bd87-83e41fe4519c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 8506238e41c5d9dac8d76d729d4919b30a0528b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23883793"
---
# <a name="sample-application-for-use-with-dmzs"></a>Ukázková aplikace pro použití s zóny DMZ
[Návrat na stránku osvědčené postupy zabezpečení hranic][HOME]

Tyto skripty prostředí PowerShell můžete spustit místně na serveru IIS01 a AppVM01 nainstalovat a nastavit jednoduché webové aplikace, který zobrazí stránku html z front-endu IIS01 server s obsahem ze serveru back-end AppVM01.

Tuto aplikaci poskytuje jednoduché testovacím prostředí pro mnoho příkladů DMZ a jak změny na koncové body, skupiny Nsg, UDR a brány Firewall pravidla může ovlivnit tok provozu.

## <a name="firewall-rule-to-allow-icmp"></a>Pravidlo brány firewall, které povolí protokol ICMP
Tento jednoduchý příkaz prostředí PowerShell můžete spustit na jakékoli virtuální počítač s Windows povolit provoz protokolu ICMP (Ping). Tato aktualizace brány firewall umožňuje snazší testování a řešení potíží s tím, že protokol ping přes bránu firewall systému windows (pro většinu distribucích systému Linux, ve které ICMP ve výchozím nastavení).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Pokud chcete použít následující skripty, přidání pravidla brány firewall je první příkaz.

## <a name="iis01---web-application-installation-script"></a>IIS01 - skript instalace webové aplikace
Bude se tento skript:

1. Otevřete IMCPv4 (Ping) na místním serveru brány windows firewall pro snazší testování
2. Instalace IIS a .net Framework v4.5
3. Vytvořit webovou stránku ASP.NET a v souboru Web.config
4. Změna fondu aplikací výchozí usnadnění přístupu k souborům
5. Nastavit pomocí účtu anonymního uživatele na váš účet správce a heslo

Tento skript prostředí PowerShell by měl být spuštěn místně při RDP měl do IIS01.

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
            <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
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

# Set App Pool to Clasic Pipeline to remote file access will work easier
    Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - soubor skriptu instalace serveru
Tento skript nastaví back-end pro tuto aplikaci jednoduché. Bude se tento skript:

1. Otevřete IMCPv4 (Ping) v bráně firewall pro snazší testování
2. Vytvořte adresář pro web
3. Vytvořte textový soubor být vzdáleně přístup webové stránky
4. Nastavení oprávnění pro adresáře a souboru pro anonymní přístup
5. Vypněte rozšířené zabezpečení Internet Exploreru umožňuje snazší procházení z tohoto serveru 

> [!IMPORTANT]
> **Nejvhodnější**: nikdy vypněte rozšířené zabezpečení Internet Exploreru na provozním serveru a obecně je vhodné procházení webu, z provozním serveru. Otevírání do sdílené složky pro anonymní přístup je také vhodné, ale done sem pro jednoduchost.
> 
> 

Tento skript prostředí PowerShell by měl být spuštěn místně při RDP měl do AppVM01. PowerShell je nutné spustit jako správce k zajištění úspěšné provedení.

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
    Write-Host "File Server Set up Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - skript instalace serveru DNS
Neexistuje žádný skript zahrnuté v této ukázkové aplikaci nastavení serveru DNS. Pokud testování pravidel brány firewall, NSG nebo UDR musí obsahovat přenosy DNS, DNS01 server musí ručně nastavit. Soubor xml konfigurace sítě a šablonou Resource Manageru pro oba příklady zahrnuje DNS01 jako primární server DNS a veřejný server DNS hostitelem úroveň 3 jako záložní server DNS. Server DNS 3 úrovně bude server DNS použitý pro jiné než místní provoz a DNS01 není nastavit, v místní síti, který by tomu bylo DNS.

## <a name="next-steps"></a>Další kroky
* Spuštění skriptu IIS01 na server služby IIS
* Spusťte skript souborového serveru na AppVM01
* Přejděte do veřejné IP adresy na IIS01 k ověření vaší sestavení

<!--Link References-->
[HOME]: ../best-practices-network-security.md
