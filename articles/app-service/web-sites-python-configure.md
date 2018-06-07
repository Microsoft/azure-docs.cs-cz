---
title: Konfigurace Python s webovými aplikacemi Azure App Service
description: Tento kurz popisuje možnosti pro vytváření obsahu a konfigurace základní serveru webové aplikace Python kompatibilní s brány rozhraní (WSGI) v Azure App Service Web Apps.
services: app-service
documentationcenter: python
tags: python
author: huguesv
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
ms.openlocfilehash: 32d9cd6c42387b67881877a1165dfcbcaef405ba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598636"
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Konfigurace Python s webovými aplikacemi Azure App Service
Tento kurz popisuje možnosti pro vytváření obsahu a konfigurace základní aplikace kompatibilní s Python Webový Server brány rozhraní (WSGI) na [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Popisuje další funkce nasazení Git, například virtuální prostředí a instalaci balíčku pomocí souboru requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django nebo Flask?
Azure Marketplace obsahuje šablony pro rozhraní Bottle, Flask a Django. Pokud vyvíjíte první webové aplikace v Azure App Service, můžete vytvořit jeden rychle z portálu Azure:

* [Webové aplikace s Bottle](https://portal.azure.com/#create/PTVS.Bottle)
* [Webovou aplikaci s rozhraním Django](https://portal.azure.com/#create/PTVS.Django)
* [Webové aplikace s Flask v systému Linux](https://portal.azure.com/#create/PTVS.FlaskLinux)

## <a name="web-app-creation-on-azure-portal"></a>Vytvoření webové aplikace v portálu Azure
Tento kurz předpokládá stávající předplatné Azure a přístup k portálu Azure.

Pokud nemáte existující webovou aplikaci, můžete vytvořit jeden z [portál Azure](https://portal.azure.com). V levém horním rohu klikněte na **vytvořit prostředek** > **Web + mobilní** > **webové aplikace**.

## <a name="git-publishing"></a>Publikování Git
Pro nově vytvořenou webovou aplikaci nakonfigurujte publikování Git podle pokynů uvedených v tématu [Místní nasazení GIT ve službě Azure App Service](app-service-deploy-local-git.md). Tento kurz používá Git pro vytváření, správu a publikování webové aplikace Python do služby Azure App Service.

Po publikování Git úložiště Git se vytvoří a přidružené k vaší webové aplikace. V úložišti adresa URL se zobrazí a dá se nabízet data z místního vývojového prostředí do cloudu. K publikování aplikací prostřednictvím Git, zkontrolujte, zda že je zároveň nainstalován klient Git a postupujte podle pokynů uvedených k webovému obsahu aplikace do služby Azure App Service.

## <a name="application-overview"></a>Přehled aplikace
V následujících částech jsou vytvořeny následující soubory. Musí být umístěny v kořenovém úložišti Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Obslužná rutina WSGI
WSGI je standard Python popsaného [období 3333](http://www.python.org/dev/peps/pep-3333/) definování rozhraní mezi webového serveru a Python. Poskytuje standardizovaná rozhraní pro zápis různé webové aplikace a rozhraní používá Python. Oblíbených webových rozhraní Python v dnešní době používá WSGI. Azure App Service Web Apps nabízí podporu pro tyto architektury; Kromě toho Pokročilí uživatelé můžete i vytvářet své vlastní tak dlouho, dokud vlastní obslužná rutina se řídí WSGI specifikace pokyny.

Tady je příklad `app.py` , který definuje vlastní obslužnou rutinu:

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

Můžete spustit tuto aplikaci místně s `python app.py`, vyhledejte `http://localhost:5555` ve webovém prohlížeči.

## <a name="virtual-environment"></a>Virtuální prostředí
I když předchozí příklad aplikace nevyžaduje žádné externí balíčky, je pravděpodobné, že vaše aplikace vyžaduje některé.

Ke správě závislosti externí balíčků nasazení Git v Azure podporuje vytváření virtuálních prostředí.

Zjistí-li Azure soubor requirements.txt v kořenovém adresáři úložiště, automaticky vytvoří virtuální prostředí s názvem `env`. K tomu dochází pouze při prvním nasazení nebo během všechna nasazení po vybrané Python runtime změnila.

Pravděpodobně chcete vytvořit virtuální prostředí pro vývoj místně, ale nemáte její zahrnutí do úložiště Git.

## <a name="package-management"></a>Správa balíčků
Ve virtuálním prostředí pomocí nástroje pip balíčky uvedené v souboru requirements.txt instalují automaticky. K tomu dojde při každém nasazení, ale pip instalaci přeskočí, pokud balíček je již nainstalován.

Příklad `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Verze Pythonu
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Příklad `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Soubor web.config
Budete muset vytvořit soubor web.config k určení, jak má server pracovat s požadavky.

Pokud máte soubor web.x.y.config souboru ve svém úložišti, kde x.y odpovídá vybraný modul runtime jazyka Python, pak Azure automaticky zkopíruje příslušný soubor jako soubor web.config.

Následující příklady web.config spoléhají na skript virtuální prostředí proxy serveru, který je popsaný v následující části.  Pracují s obslužná rutina WSGI použitých v tomto příkladu `app.py` výše.

Příklad `web.config` pro jazyk Python 2.7:

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


Příklad `web.config` pro jazyk Python 3.4:

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


Statické soubory jsou zpracovávány pro webový server přímo, aniž by bylo nutné prostřednictvím kódu Pythonu pro zlepšení výkonu.

V předchozích ukázkách umístění statické soubory na disku by měl odpovídat umístění v adrese URL. To znamená, že žádost o `http://pythonapp.azurewebsites.net/static/site.css` bude sloužit k souboru na disku v `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` je, kde můžete určit WSGI obslužná rutina. V předchozích ukázkách má `app.wsgi_app` protože rutina je funkce s názvem `wsgi_app` v `app.py` v kořenové složce.

`PYTHONPATH` můžete přizpůsobit, ale pokud nainstalujete všechny závislosti ve virtuálním prostředí zadáním v souboru requirements.txt, neměli byste potřebovat změnit.

## <a name="virtual-environment-proxy"></a>Virtuální prostředí Proxy
Následující skript se používá k načtení obslužná rutina WSGI, aktivujte virtuální prostředí a protokolu chyb. Je určený jako obecný a použít bez úprav.

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


## <a name="customize-git-deployment"></a>Přizpůsobení nasazení Git
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Řešení potíží – instalace balíčku
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Řešení potíží – virtuální prostředí
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Další postup
Další informace naleznete ve [Středisku pro vývojáře Python](/develop/python/).

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 
