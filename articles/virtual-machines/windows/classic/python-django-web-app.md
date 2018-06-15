---
title: Webové aplikace Django na virtuální počítač Windows serveru Azure | Microsoft Docs
description: Zjistěte, jak k hostování webu na základě Django v Azure pomocí virtuální počítač s Windows Server 2012 R2 Datacenter s modelem nasazení classic.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2017
ms.locfileid: "24057014"
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Django Hello World webové aplikace na virtuální počítač Windows serveru

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a modelu nasazení classic](../../../resource-manager-deployment-model.md). Tento článek popisuje model nasazení classic. Doporučujeme vám, že většina nových nasazení používala model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V tomto kurzu se dozvíte, jak k hostování webu na základě Django v systému Windows Server v Azure Virtual Machines. V tomto kurzu předpokládáme žádné předchozí zkušenosti s Azure. Po dokončení tohoto kurzu, může mít jiné aplikace založené na rozhraní Django nahoru a běží v cloudu.

Naučte se:

* Nastavte virtuální počítač Azure na hostitele Django. I když tento kurz vysvětluje, jak to udělat pro **systému Windows Server**, můžete provést stejný pro virtuální počítač Linux hostované v Azure.
* Vytvořte novou aplikaci Django v systému Windows.

Tento kurz ukazuje, jak pro vytvoření základní webové aplikace Hello World. Aplikace je hostitelem virtuálního počítače Azure.

Na následujícím snímku obrazovky je vidět hotová aplikace:

![Okno prohlížeče zobrazí stránku hello world v Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Vytvoření a nastavení virtuálního počítače Azure na hostitele Django

1. Pokud chcete vytvořit virtuální počítač Azure s Windows Server 2012 R2 Datacenter distribuce, najdete v části [vytvoření virtuálního počítače se systémem Windows na portálu Azure](tutorial.md).
2. Nastavení pro přesměrování portu 80 provoz z webu na portu 80 pro virtuální počítač Azure:
   
   1. Na portálu Azure přejděte do řídicího panelu a vyberte nově vytvořenou virtuálního počítače.
   2. Klikněte na **Koncové body** a potom na **Přidat**.

     ![Přidání koncového bodu](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. Na **přidání koncového bodu** stránky, pro **název**, zadejte **HTTP**. Nastavte TCP veřejné a privátní porty na **80**.

     ![Zadejte název a nastavte veřejné a privátní porty](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Klikněte na **OK**.
     
3. Na řídicím panelu vyberte virtuální počítač. Chcete-li vzdáleně přihlašují k nově vytvořený virtuální počítač Azure pomocí protokolu RDP (Remote Desktop), klikněte na tlačítko **Connect**.  

> [!IMPORTANT] 
> Tyto pokyny předpokládají, že jste přihlášení k virtuálnímu počítači správně. Také se předpokládá, že k vydání příkazů ve virtuálním počítači a není v místním počítači.

## <a id="setup"></a>Instalace Python, rozhraní Django a WFastCGI
> [!NOTE]
> Chcete-li stáhnout v aplikaci Internet Explorer, možná budete muset nakonfigurovat Internet Explorer **konfigurace rozšířeného zabezpečení aplikace** nastavení. Chcete-li to provést, klikněte na tlačítko **spustit** > **nástroje pro správu** > **správce serveru** > **místní Server**. Klikněte na tlačítko **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**a potom vyberte **vypnout**.

1. Instalace nejnovější verze Python 2.7 nebo 3.4 Python z [python.org][python.org].
2. Instalovat balíčky wfastcgi a django, pomocí nástroje pip.
   
    Python 2.7 použijte následující příkaz:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Pro Python 3.4 použijte následující příkaz:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Nainstalovat službu ISS s FastCGI
* Instalace Internetové informační služby (IIS) s podporou rozhraní FastCGI. To může trvat několik minut provést.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Vytvořte novou aplikaci Django
1. V C:\inetpub\wwwroot Pokud chcete vytvořit nový projekt Django, zadejte následující příkaz:
   
   Python 2.7 použijte následující příkaz:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Pro Python 3.4 použijte následující příkaz:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Výsledek příkazu New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. `django-admin` Příkaz generuje základní strukturu pro weby založené na rozhraní Django:
   
   * `helloworld\manage.py`umožňuje hostování spuštění a zastavení hostování svého webu na základě Django.
   * `helloworld\helloworld\settings.py`má Django nastavení pro vaši aplikaci.
   * `helloworld\helloworld\urls.py`má kód mapování mezi každou adresu URL a jeho zobrazení.
3. V adresáři C:\inetpub\wwwroot\helloworld\helloworld vytvořte nový soubor s názvem views.py. Tento soubor obsahuje zobrazení, který vykreslí stránku "hello, world". V editoru kódu zadejte následující příkazy:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Nahraďte obsah souboru urls.py pomocí následujících příkazů:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Nastavení služby IIS
1. V souboru applicationhost.config globální odemkněte oddíl obslužné rutiny.  To umožňuje používat obslužná rutina Python souboru web.config. Přidejte tento příkaz:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Aktivujte WFastCGI. Tento postup přidá aplikaci do souboru applicationhost.config globální, který odkazuje na vaše překladač Pythonu spustitelný soubor a wfastcgi.py skript.
   
    V Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    V Pythonu 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. V C:\inetpub\wwwroot\helloworld vytvořte soubor web.config. Hodnota `scriptProcessor` atribut by měl odpovídat výstup z předchozího kroku. Další informace o nastavení wfastcgi najdete v tématu [úložiště pypi wfastcgi][wfastcgi].
   
   V Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
   V Pythonu 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Aktualizujte umístění na výchozí web služby IIS tak, aby odkazoval na složku projekt Django:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Načtěte webovou stránku v prohlížeči.

![Okno prohlížeče zobrazí stránku hello world v Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Vypněte virtuální počítač Azure
Po dokončení v tomto kurzu, doporučujeme vypnout nebo odeberte virtuální počítač Azure jste vytvořili pro tento kurz. Tím uvolní prostředky pro ostatní kurzy a účtovány poplatky za používání Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
