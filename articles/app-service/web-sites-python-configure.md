---
title: Konfigurace Pythonu pomocí služby Azure App Service Web Apps
description: Tento kurz popisuje možnosti pro vytváření a konfiguraci brány rozhraní s rozhraním WSGI () aplikaci v Pythonu kompatibilní základní webový server na Azure App Service Web Apps.
services: app-service
documentationcenter: python
tags: python
author: cephalin
manager: erikre
editor: ''
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: 796de682df28c28bc66f2409e486dfdc221aedd1
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42060745"
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Konfigurace Pythonu pomocí služby Azure App Service Web Apps
Tento kurz popisuje možnosti pro vytváření a konfigurovat základní aplikaci Pythonu kompatibilní Web Server Gateway Interface s rozhraním WSGI () na [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Popisuje další funkce nasazení z Gitu, jako je například virtuální prostředí a instalace balíčku používání souboru requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django nebo Flask?
Na webu Azure Marketplace obsahuje šablony pro rozhraní Bottle, Django a Flask. Pokud vyvíjíte vaší první webové aplikace ve službě Azure App Service, můžete vytvořit jednu rychle z portálu Azure portal:

* [Webové aplikace pomocí Bottle v Linuxu](https://portal.azure.com/#create/PTVS.BottleLinux)
* [Webové aplikace pomocí Django v Linuxu](https://portal.azure.com/#create/PTVS.DjangoLinux)
* [Webové aplikace pomocí Flask v Linuxu](https://portal.azure.com/#create/PTVS.FlaskLinux)

Nebo můžete [prozkoumat sami na webu Azure Marketplace](https://portal.azure.com/#create/hub).

## <a name="web-app-creation-on-azure-portal"></a>Vytvoření webové aplikace na webu Azure portal
Tento kurz předpokládá stávající předplatné Azure a přístup k webu Azure portal.

Pokud nemáte stávající webovou aplikaci, můžete vytvořit jeden z [webu Azure portal](https://portal.azure.com). V levém horním rohu klikněte na tlačítko **vytvořit prostředek** > **Web + mobilní zařízení** > **webovou aplikaci**.

## <a name="git-publishing"></a>Publikování Git
Pro nově vytvořenou webovou aplikaci nakonfigurujte publikování Git podle pokynů uvedených v tématu [Místní nasazení GIT ve službě Azure App Service](app-service-deploy-local-git.md). Tento kurz používá Git k vytvoření, správě a publikování vaší webové aplikace v Pythonu do služby Azure App Service.

Po nastavení publikování Git se úložiště Git a související s vaší webovou aplikací. Adresa URL úložiště se zobrazí a slouží k zápisu dat z místního vývojového prostředí do cloudu. Publikování aplikací prostřednictvím Git, ujistěte se, že klient Git je rovněž nainstalován a postupujte podle pokynů uvedených tak, aby nabízel obsah vaší webové aplikace do služby Azure App Service.

## <a name="application-overview"></a>Přehled aplikace
V následujících částech jsou vytvořeny následující soubory. Musí být umístěné ve kořenového adresáře úložiště Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Obslužná rutina s rozhraním WSGI
S rozhraním WSGI je standard Python popsal [období 3333](http://www.python.org/dev/peps/pep-3333/) definování rozhraní mezi webovým serverem a Python. Poskytuje standardizované rozhraní pro zápis různých webových aplikací a architektur pomocí Pythonu. Oblíbené webové architektury Python pomocí ještě dnes s rozhraním WSGI. Azure App Service Web Apps poskytuje podporu pro takové architektury; Kromě toho Pokročilí uživatelé můžete dokonce vytvářet vlastní tak dlouho, dokud tato vlastní obslužná rutina řídí specifikaci pokyny s rozhraním WSGI.

Tady je příklad `app.py` , který definuje vlastní obslužné rutiny:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Můžete tuto aplikaci místně spustíte `python app.py`, vyhledejte `http://localhost:5555` ve webovém prohlížeči.

## <a name="virtual-environment"></a>Virtuální prostředí
Ačkoli předchozí příklad aplikace nevyžaduje, aby veškeré případné externí balíčky, je pravděpodobné, že vaše aplikace vyžaduje některé.

Nasazení z Gitu Azure ke správě externího balíčku závislosti, podporuje vytváření virtuálních prostředí.

Když Azure zjistí soubor requirements.txt v kořenové složce úložiště, se automaticky vytvoří virtuální prostředí s názvem `env`. K tomu dochází pouze při prvním nasazení nebo během každého nasazení po vybrané Python runtime změnila.

Pravděpodobně chcete vytvořit virtuální prostředí pro vývoj, ale nezahrne ho v úložišti Git.

## <a name="package-management"></a>Správa balíčků
Balíčky uvedené v souboru requirements.txt jsou nainstalovány automaticky ve virtuálním prostředí pomocí nástroje pip. To se stane při každém nasazení, ale pip instalaci přeskočí, pokud balíček je už nainstalovaný.

Příklad `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Verze Pythonu
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Příklad `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Soubor web.config
Je potřeba vytvořit soubor web.config k určení, jak má server pracovat s požadavky.

Pokud máte soubor web.x.y.config souboru v úložišti, kde x.y odpovídá vybraný modul runtime Python, pak Azure automaticky zkopíruje odpovídající soubor jako soubor web.config.

Následující příklady web.config závisí na virtuální prostředí skriptu proxy serveru, která je popsána v následující části.  Při práci s obslužnou rutinu s rozhraním WSGI použitých v příkladu `app.py` výše.

Příklad `web.config` pro Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Příklad `web.config` pro Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Statické soubory jsou zpracovány webovým serverem přímo, bez nutnosti kontaktovat za účelem vylepšení výkonu kódu Python.

V předchozích ukázkách umístění statické soubory na disku by měl odpovídat umístění v adrese URL. To znamená, že žádost o `http://pythonapp.azurewebsites.net/static/site.css` bude sloužit k souboru na disku na `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` je, kde můžete určit obslužnou rutinu s rozhraním WSGI. V předchozích příkladech má `app.wsgi_app` protože rutina je funkce s názvem `wsgi_app` v `app.py` v kořenové složce.

`PYTHONPATH` je možné přizpůsobit, ale pokud nainstalujete všechny závislosti ve virtuálním prostředí tak, že zadáte v souboru requirements.txt, neměli byste potřebovat ho změnit.

## <a name="virtual-environment-proxy"></a>Virtuální prostředí Proxy
Následující skript slouží k načtení obslužná rutina s rozhraním WSGI, aktivovat virtuální prostředí a protokolu chyb. To je navržena jako obecný a používaných bez úprav.

Obsah `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []

        site.main()

        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Přizpůsobuje nasazení Git
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Řešení potíží – instalace balíčku
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Řešení potíží – virtuální prostředí
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="troubleshooting---startup-errors"></a>Řešení potíží – chyby při spuštění
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="next-steps"></a>Další postup
Další informace naleznete ve [Středisku pro vývojáře Python](https://azure.microsoft.com/develop/python/).

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 
