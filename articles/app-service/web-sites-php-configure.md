---
title: Konfigurace PHP ve službě Azure App Service Web Apps
description: Zjistěte, jak nakonfigurovat výchozí instalace PHP nebo přidat vlastní instalace PHP pro službu Web Apps ve službě Azure App Service.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 39c40482017ae0c0dedcfb1b65ff3767c4e45169
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999184"
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Konfigurace PHP ve službě Azure App Service Web Apps

## <a name="introduction"></a>Úvod

Tento průvodce vám ukáže, jak nakonfigurovat integrované modulu runtime PHP pro službu Web Apps v [služby Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), poskytnout vlastní PHP runtime a povolit rozšíření. Pokud chcete používat App Service, zaregistrujte si [bezplatnou zkušební verzi]. Získat maximum z této příručce, byste nejprve vytvořit webové aplikace v PHP ve službě App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Postupy: Změna integrované verze PHP

Ve výchozím nastavení PHP 5.6 je nainstalována a okamžitě k dispozici pro použití při vytváření webové aplikace služby App Service. Nejlepší způsob, jak zobrazit dostupné verze revize, její výchozí konfiguraci a povolené rozšíření je k nasazení skriptu, který volá [phpinfo()] funkce.

Verze PHP 7.0 a PHP 7.2 jsou také k dispozici, ale není ve výchozím nastavení povolená. Aktualizovat verzi PHP, postupujte podle jednoho z těchto metod:

### <a name="azure-portal"></a>portál Azure

1. Přejděte do webové aplikace [webu Azure portal](https://portal.azure.com) a klikněte na **nastavení** tlačítko.

    ![Nastavení webové aplikace][settings-button]
1. Z **nastavení** okně vyberte **nastavení aplikace** a vyberte novou verzi PHP.

    ![Nastavení aplikace][application-settings]
1. Klikněte na tlačítko **Uložit** tlačítko v horní části **nastavení webové aplikace** okno.

    ![Uložit nastavení konfigurace][save-button]

### <a name="azure-powershell-windows"></a>Prostředí Azure PowerShell (Windows)

1. Otevřete prostředí Azure PowerShell a přihlašovací jméno ke svému účtu:

        PS C:\> Connect-AzureRmAccount
1. Nastavení verze PHP pro webovou aplikaci.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. Verze PHP je nyní nastaveno. Můžete potvrdit, tato nastavení:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Azure CLI 

Pokud chcete používat rozhraní příkazového řádku Azure, je nutné [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ve vašem počítači.

1. Otevřete okno terminálu a přihlašovací jméno ke svému účtu.

        az login

1. Zaškrtněte, pokud chcete zobrazit seznam podporovaných modulů runtime.

        az webapp list-runtimes | grep php

1. Nastavení verze PHP pro webovou aplikaci.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. Verze PHP je nyní nastaveno. Můžete potvrdit, tato nastavení:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Postupy: Změna předdefinovaných konfigurací PHP

Pro všechny předdefinované PHP runtime můžete změnit některé možnosti konfigurace pomocí následujících kroků. (Informace o souboru php.ini direktivy najdete v tématu [Seznam php.ini direktivy].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Změna PHP\_INI\_uživatele, PHP\_INI\_PERDIR, PHP\_INI\_všechna nastavení konfigurace

1. Přidat [. user.ini] souboru do kořenového adresáře.
1. Přidat nastavení konfigurace `.user.ini` soubor pomocí stejné syntaxe, kterou použijete v `php.ini` souboru. Například, pokud chcete zapnout `display_errors` nastavení a nastavte `upload_max_filesize` nastavení na 10 milionů, vaše `.user.ini` soubor bude obsahovat tento text:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. Nasazení webové aplikace.
1. Restartujte webovou aplikaci. (Restartování je nezbytné, protože četnost s PHP, který čte `.user.ini` soubory se vztahují `user_ini.cache_ttl` nastavení, která je nastavení úrovně systému a ve výchozím nastavení je 300 sekund (5 minut). Restartování webové aplikace PHP k načtení nové nastavení v vynutí `.user.ini` souboru.)

Jako alternativu k použití `.user.ini` soubor, můžete použít [ini_set()] funkce v skripty pro nastavení možností konfigurace, které nejsou direktivy úrovni systému.

### <a name="changing-phpinisystem-configuration-settings"></a>Změna PHP\_INI\_nastavení konfigurace systému

1. Přidat nastavení aplikace do webové aplikace s klíčem `PHP_INI_SCAN_DIR` a hodnota `d:\home\site\ini`
1. Vytvoření `settings.ini` soubor pomocí konzoly Kudu (http://&lt;název lokality&gt;. scm.azurewebsite.net) v `d:\home\site\ini` adresáře.
1. Přidat nastavení konfigurace `settings.ini` soubor pomocí stejné syntaxe, kterou použijete v `php.ini` souboru. Například, pokud jste chtěli tak, aby odkazoval `curl.cainfo` nastavení na hodnotu `*.crt` souborů a nastavení "wincache.maxfilesize" na 512 kB, vaše `settings.ini` soubor bude obsahovat tento text:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Chcete-li znovu načíst změny, restartujte webovou aplikaci.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Postupy: povolení rozšíření v výchozího PHP runtime

Jak je uvedeno v předchozí části, je nejlepší způsob, jak zobrazit výchozí verzi PHP, její výchozí konfiguraci a povolené rozšíření nasazení skriptu, který volá [phpinfo()]. Chcete-li povolit další rozšíření, pomocí následujících kroků:

### <a name="configure-via-ini-settings"></a>Konfigurace prostřednictvím nastavení ini

1. Přidat `ext` do adresáře `d:\home\site` adresáře.
1. Vložit `.dll` soubory v rozšíření `ext` adresář (třeba `php_xdebug.dll`). Ujistěte se, že rozšíření jsou kompatibilní s výchozí verzi PHP a jsou VC9 a kompatibilní bez bezpečným pro vlákno (nts).
1. Přidat nastavení aplikace do webové aplikace s klíčem `PHP_INI_SCAN_DIR` a hodnota `d:\home\site\ini`
1. Vytvoření `ini` ve `d:\home\site\ini` volá `extensions.ini`.
1. Přidat nastavení konfigurace `extensions.ini` soubor pomocí stejné syntaxe, kterou použijete v `php.ini` souboru. Například, pokud chcete povolit rozšíření MongoDB a nástroje XDebug vaše `extensions.ini` soubor bude obsahovat tento text:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Restartujte webovou aplikaci, načíst změny.

### <a name="configure-via-app-setting"></a>Konfigurace prostřednictvím nastavení aplikace

1. Přidat `bin` adresáře do kořenového adresáře.
1. Vložit `.dll` soubory v rozšíření `bin` adresář (třeba `php_xdebug.dll`). Ujistěte se, že rozšíření jsou kompatibilní s výchozí verzi PHP a jsou VC9 a kompatibilní bez bezpečným pro vlákno (nts).
1. Nasazení webové aplikace.
1. Přejděte do webové aplikace na webu Azure Portal a klikněte na **nastavení** tlačítko.

    ![Nastavení webové aplikace][settings-button]
1. Z **nastavení** okno, vyberte **nastavení aplikace** a přejděte **nastavení aplikace** oddílu.
1. V **nastavení aplikace** části, vytvořte **PHP_EXTENSIONS** klíč. Hodnota pro tento klíč bude cestu vzhledem ke kořenové složky webu: **bin\your ext, přípona souboru**.

    ![Povolení rozšíření v nastavení aplikace][php-extensions]
1. Klikněte na tlačítko **Uložit** tlačítko v horní části **nastavení webové aplikace** okno.

    ![Uložit nastavení konfigurace][save-button]

Zend rozšíření jsou také podporovány pomocí **PHP_ZENDEXTENSIONS** klíč. Pokud chcete povolit více rozšíření, zahrnují čárkou oddělený seznam `.dll` soubory pro hodnotu nastavení aplikace.

## <a name="how-to-use-a-custom-php-runtime"></a>Postupy: použití vlastního modulu runtime PHP

Namísto výchozího PHP runtime App Service Web Apps můžete použít modul runtime PHP, který zadáte pro spouštění skriptů PHP. Modul runtime, které poskytujete se dá nakonfigurovat pomocí `php.ini` soubor, který je rovněž poskytnout. Použití vlastního modulu runtime PHP s Web Apps, následujícím postupem.

1. Získáte vláknově bezpečné, VC9 nebo VC11 kompatibilní verzi PHP pro Windows. Nejnovější verze PHP pro Windows najdete tady: [ http://windows.php.net/download/ ]. Starší verze najdete v archivu tady: [ http://windows.php.net/downloads/releases/archives/ ].
1. Upravit `php.ini` soubor pro prostředí runtime. Nastavení konfigurace, která jsou systémové úrovni – jen direktivy jsou ignorovány ve webových aplikacích. (Informace o systému úroveň jen direktivy najdete v tématu [Seznam php.ini direktivy]).
1. Volitelně přidejte rozšíření do vašeho prostředí PHP runtime a povolit je `php.ini` souboru.
1. Přidat `bin` do kořenového adresáře a put adresáře, který obsahuje prostředí PHP runtime v něm adresáře (například `bin\php`).
1. Nasazení webové aplikace.
1. Přejděte do webové aplikace na webu Azure Portal a klikněte na **nastavení** tlačítko.

    ![Nastavení webové aplikace][settings-button]
1. Z **nastavení** okno, vyberte **nastavení aplikace** a přejděte **mapování obslužných rutin** oddílu. Přidat `*.php` rozšíření pole a přidejte cestu k `php-cgi.exe` spustitelný soubor. Když vložíte do vašeho prostředí runtime PHP `bin` je cesta k adresáři v kořenovém adresáři aplikace `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Zadejte rutinu v mapování obslužných rutin][handler-mappings]
1. Klikněte na tlačítko **Uložit** tlačítko v horní části **nastavení webové aplikace** okno.

    ![Uložit nastavení konfigurace][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Postupy: povolení automatizace nástroje Composer v Azure

Ve výchozím nastavení služby App Service, nebude dělat nic s composer.json, pokud máte ve vašem projektu PHP. Pokud používáte [nasazení z Gitu](app-service-deploy-local-git.md), můžete povolit při zpracování souboru composer.json `git push` tím, že rozšíření autora.

> [!NOTE]
> Je možné [Hlasujte pro prvotřídní Composer podporu ve službě App Service zde](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Ve vaší PHP webové okně aplikace [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **nástroje** > **rozšíření**.

    ![Okno Nastavení portálu Azure portal k povolení automatizace nástroje Composer v Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klikněte na tlačítko **přidat**, pak klikněte na tlačítko **Composer**.

    ![Přidat rozšíření autora k povolení automatizace nástroje Composer v Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Klikněte na tlačítko **OK** přijměte právní podmínky. Klikněte na tlačítko **OK** znovu přidáte rozšíření.

    **Nainstalovaná rozšíření** okno rozšíření autora.
    ![Přijměte právní podmínky pro povolení automatizace nástroje Composer v Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Teď v okně terminálu na svém místním počítači provádět `git add`, `git commit`, a `git push` do vaší webové aplikace. Všimněte si, že závislosti definované v souboru composer.json instaluje autora.

    ![Nasazení z Gitu s automatizace nástroje Composer v Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
>

[bezplatnou zkušební verzi]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Seznam php.ini direktivy]: http://www.php.net/manual/en/ini.list.php
[. user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
