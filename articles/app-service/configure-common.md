---
title: Konfigurace aplikací na portálu – Azure App Service
description: Naučte se konfigurovat společná nastavení pro aplikaci služby App Service na webu Azure Portal.
keywords: služby Azure app service, webové aplikace, nastavení aplikace, proměnné prostředí
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957907"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Nakonfigurovat aplikaci služby App Service na webu Azure Portal

Toto téma vysvětluje, jak nakonfigurovat nastavení pro webové aplikace, mobilní back-end nebo aplikace pomocí rozhraní API [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurace nastavení aplikace

Ve službě App Service použijte nastavení aplikace jako proměnné prostředí. V [Azure Portal], přejděte na stránku správy vaší aplikace. V nabídce vlevo aplikaci, klikněte na tlačítko **konfigurace** > **nastavení aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

Pro vývojáře v ASP.NET a ASP.NET Core, nastavení aplikace ve službě App Service je třeba nastavit `<appSettings>` v *Web.config*, ale přepsat hodnoty ve službě App Service v *Web.config*. Můžete ponechat nastavení vývoje (třeba heslo místního MySQL) *Web.config*, ale výrobní tajné kódy (třeba heslo databáze Azure MySQL) bezpečné ve službě App Service. Stejný kód používá nastavení pro vývoj, Jestliže ladíte místně a použije vaše produkční tajných kódů při nasazení do Azure.

Další balíčky jazyka, získat nastavení aplikace jako proměnné prostředí za běhu. Konkrétní kroky zásobník jazyka najdete v tématu:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](containers/configure-custom-container.md#configure-environment-variables)

Nastavení aplikace se vždy šifrují, pokud uložená (šifrovaná at-rest).

> [!NOTE]
> Nastavení aplikace lze také vyřešit z [služby Key Vault](/azure/key-vault/) pomocí [služby Key Vault odkazuje](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Zobrazit skryté hodnoty

Ve výchozím nastavení jsou skryté hodnoty pro nastavení aplikace na portálu pro zabezpečení. Chcete-li zobrazit skryté hodnoty nastavení aplikace, klikněte na tlačítko **hodnotu** pole daného nastavení. Chcete-li zobrazit hodnoty všech nastavení aplikace, klikněte na tlačítko **zobrazit hodnotu** tlačítko.

### <a name="add-or-edit"></a>Přidat nebo upravit

Chcete-li přidat nové nastavení aplikace, klikněte na tlačítko **nové nastavení aplikace**. V dialogovém okně můžete [zůstat nastavení na aktuální pozici](deploy-staging-slots.md#which-settings-are-swapped).

Chcete-li upravit nastavení, klikněte na tlačítko **upravit** tlačítko na pravé straně.

Až budete hotovi, klikněte na tlačítko **aktualizace**. Nezapomeňte kliknout na **Uložit** zpátky **konfigurace** stránky.

> [!NOTE]
> V kontejneru Linuxu výchozí nebo vlastní kontejner Linuxu, jako jsou všechny vnořené klíče strukturu JSON v název nastavení aplikace `ApplicationInsights:InstrumentationKey` je potřeba nakonfigurovat ve službě App Service jako `ApplicationInsights__InstrumentationKey` název klíče. Jinými slovy, všechny `:` by měl být nahrazen `__` (dvojitým podtržítkem).
>

### <a name="edit-in-bulk"></a>Upravit hromadně

Chcete-li přidat nebo upravit nastavení aplikace hromadně, klikněte na tlačítko **rozšířené úpravy** tlačítko. Až budete hotovi, klikněte na tlačítko **aktualizace**. Nezapomeňte kliknout na **Uložit** zpátky **konfigurace** stránky.

Nastavení aplikace mít následující formát JSON:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Konfigurace připojovacích řetězců

V [Azure Portal], přejděte na stránku správy vaší aplikace. V nabídce vlevo aplikaci, klikněte na tlačítko **konfigurace** > **nastavení aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

Pro vývojáře v ASP.NET a ASP.NET Core, nastavení připojovacích řetězců ve službě App Service je třeba nastavit `<connectionStrings>` v *Web.config*, ale přepíše hodnoty nastavené ve službě App Service v *Web.config*. Můžete ponechat nastavení vývoje (například soubor databáze) *Web.config* a produkční tajné kódy (třeba přihlašovací údaje SQL Database) bezpečné ve službě App Service. Stejný kód používá nastavení pro vývoj, Jestliže ladíte místně a použije vaše produkční tajných kódů při nasazení do Azure.

Pro další balíčky jazyka je vhodnější použít [nastavení aplikace](#configure-app-settings) místo toho, že připojovacích řetězců vyžadují zvláštní formátování proměnné klíče v pořadí pro přístup k hodnotám. Tady je jedinou výjimkou však: určité typy Azure database zálohovaly spolu s aplikace při konfiguraci jejich připojovací řetězce ve vaší aplikaci. Další informace najdete v tématu [co se zálohuje](manage-backup.md#what-gets-backed-up). Pokud není nutné tento automatické zálohování, použijte nastavení aplikace.

Připojovací řetězce za běhu, jsou k dispozici jako proměnné prostředí, předponu následujících typů připojení:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* Vlastní: `CUSTOMCONNSTR_`

Například s názvem připojovací řetězec MySql *connectionstring1* mohou být přístupné jako proměnnou prostředí `MYSQLCONNSTR_connectionString1`. Konkrétní kroky zásobník jazyka najdete v tématu:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](containers/configure-custom-container.md#configure-environment-variables)

Připojovací řetězce se vždy šifrují, pokud uložená (šifrovaná at-rest).

> [!NOTE]
> Připojovací řetězce lze také vyřešit z [služby Key Vault](/azure/key-vault/) pomocí [služby Key Vault odkazuje](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Zobrazit skryté hodnoty

Ve výchozím nastavení hodnoty pro funkci připojovací řetězce jsou skryté v portálu pro zabezpečení. Chcete-li zobrazit skryté hodnotu připojovacího řetězce, stačí kliknout **hodnotu** pole řetězce. Chcete-li zobrazit hodnoty všech připojovací řetězce, klikněte na tlačítko **zobrazit hodnotu** tlačítko.

### <a name="add-or-edit"></a>Přidat nebo upravit

Chcete-li přidat nový připojovací řetězec, klikněte na tlačítko **nový připojovací řetězec**. V dialogovém okně můžete [držet připojovacího řetězce aktuálním slotu](deploy-staging-slots.md#which-settings-are-swapped).

Chcete-li upravit nastavení, klikněte na tlačítko **upravit** tlačítko na pravé straně.

Až budete hotovi, klikněte na tlačítko **aktualizace**. Nezapomeňte kliknout na **Uložit** zpátky **konfigurace** stránky.

### <a name="edit-in-bulk"></a>Upravit hromadně

Chcete-li přidat nebo upravit připojovací řetězce hromadně, klikněte na tlačítko **rozšířené úpravy** tlačítko. Až budete hotovi, klikněte na tlačítko **aktualizace**. Nezapomeňte kliknout na **Uložit** zpátky **konfigurace** stránky.

Připojovací řetězce mají následující formátování JSON:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Konfigurace obecných nastavení

V [Azure Portal], přejděte na stránku správy vaší aplikace. V nabídce vlevo aplikaci, klikněte na tlačítko **konfigurace** > **nastavení aplikace**.

![Obecná nastavení](./media/configure-common/open-general.png)

Tady můžete nakonfigurovat některé obecná nastavení pro aplikaci. Některá nastavení vyžadují, abyste [škálování až na vyšší cenové úrovně](web-sites-scale.md).

- **Stack – nastavení**: Softwarový zásobník pro spuštění aplikace, včetně jazyka a verze sady SDK. Pro Linuxové aplikace a aplikace vlastní kontejner můžete také nastavit soubor nepovinný spouštěcí příkaz, který nebo soubor.
- **Nastavení platformy**: Umožňuje konfigurovat nastavení pro hostitelskou platformu, včetně:
    - **Bitová verze**: 32bitová nebo 64bitová verze.
    - **Protokol WebSocket**: Pro [funkce SignalR technologie ASP.NET] nebo [socket.io](https://socket.io/), např.
    - **Always On**: Nechte aplikaci načíst i v případě, že neexistuje žádný provoz. Musíte ji povolit pro průběžné WebJobs nebo webových úloh, které jsou aktivované pomocí výrazů CRON.
    - **Spravovaná verze kanálu**: IIS [režim kanálů]. Nastavte ho na **Classic** Pokud máte starší verzi aplikace, která vyžaduje starší verzi služby IIS.
    - **Verze protokolu HTTP**: Nastavte na **2.0** povolení podpory pro [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokolu.
    > [!NOTE]
    > Většina moderních prohlížečů podporují protokol HTTP/2 přes protokol TLS pouze, zatímco stále nešifrované přenosy pomocí protokolu HTTP/1.1. Chcete-li zajistit, že klientské prohlížeče připojit k vaší aplikace pomocí protokolu HTTP/2, buď [koupit certifikát App Service](web-sites-purchase-ssl-web-site.md) pro vlastní doménu aplikace nebo [vytvoření vazby certifikátu třetích stran](app-service-web-tutorial-custom-ssl.md).
    - **Spřažení směrování žádostí na aplikace**: V nasazení s více instancemi Ujistěte se, že klient je směrovat do stejné instance po dobu trvání relace. Tuto možnost můžete nastavit **vypnout** pro bezstavové aplikace.
- **Ladění**: Povolení vzdáleného ladění pro [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), nebo [Node.js](containers/configure-language-nodejs.md#debug-remotely) aplikace. Tato volba vypne automaticky po 48 hodinách.
- **Příchozí klientské certifikáty**: Vyžadovat klientské certifikáty v [vzájemného ověřování](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurace výchozích dokumentů

Toto nastavení platí jenom pro aplikace Windows.

V [Azure Portal], přejděte na stránku správy vaší aplikace. V nabídce vlevo aplikaci, klikněte na tlačítko **konfigurace** > **výchozí dokumenty**.

![Obecná nastavení](./media/configure-common/open-documents.png)

Výchozí dokument je webová stránka, která se zobrazí na adrese URL kořenového webu. V seznamu první odpovídající soubor se používá. Chcete-li přidat nový výchozí dokument, klikněte na tlačítko **nový dokument**. Nezapomeňte kliknout na **Uložit**.

Pokud aplikace využívá moduly, které směrují na základě adresy URL místo obsluhuje statický obsah, není nutné pro výchozí dokumenty.

## <a name="configure-path-mappings"></a>Cesta k mapování konfigurace

V [Azure Portal], přejděte na stránku správy vaší aplikace. V nabídce vlevo aplikaci, klikněte na tlačítko **konfigurace** > **cesta k mapování**.

![Obecná nastavení](./media/configure-common/open-path.png)

**Cesta k mapování** stránce se zobrazují různé věci podle typu operačního systému.

### <a name="windows-apps-uncontainerized"></a>Aplikace Windows (uncontainerized)

Pro aplikace Windows můžete upravit mapování obslužných rutin služby IIS a virtuální aplikace a adresáře.

Mapování obslužných rutin umožňují přidat procesorů pro vlastní skripty pro zpracování požadavků pro konkrétní přípony souborů. Chcete-li přidat vlastní obslužnou rutinu, klikněte na tlačítko **novou obslužnou rutinu**. Obslužná rutina nakonfigurujte následujícím způsobem:

- **Rozšíření**. Přípona souboru, zpracování, jako například  *\*.php* nebo *handler.fcgi*.
- **Procesor skriptů**. Absolutní cesta mapě skriptů pro vás. Požadavky na soubory, které odpovídají přípona souboru jsou zpracovány mapě skriptů. Použijte cestu `D:\home\site\wwwroot` k odkazování na kořenovém adresáři vaší aplikace.
- **Argumenty**. Volitelné argumenty příkazového řádku k mapě skriptů.

Každá aplikace má výchozí kořenové cestě (`/`) mapovat na `D:\home\site\wwwroot`, ve kterém je váš kód nasazen ve výchozím nastavení. Je-li v kořenovém adresáři aplikace je v jiné složce, nebo pokud vaše úložiště má více než jednu aplikaci, můžete upravit nebo přidat virtuální aplikace a tady adresáře. Klikněte na tlačítko **novou virtuální aplikaci nebo adresář**.

Pokud chcete nakonfigurovat virtuální aplikace a adresáře, zadejte každý virtuální adresář a jeho odpovídající fyzickou cestu vzhledem ke kořenové složky webu (`D:\home`). Volitelně můžete vybrat **aplikace** zaškrtávacího políčka označíte virtuální adresář jako aplikace.

### <a name="containerized-apps"></a>Kontejnerizovaných aplikací

Je možné [přidat vlastní úložiště pro kontejnerizované aplikace](containers/how-to-serve-content-from-azure-storage.md). Kontejnerizované aplikace obsahují všechny Linuxových aplikací a také vlastní kontejnery Windows a Linuxu spuštěné ve službě App Service. Klikněte na tlačítko **nové připojení úložiště Azure** a nakonfigurujte vlastní úložiště následujícím způsobem:

- **Název**: Zobrazovaný název.
- **Možnosti konfigurace**: **Základní** nebo **Advanced**.
- **Účty úložiště**: Účet úložiště s kontejnerem, že je vhodné.
- **Typ úložiště**: **Objekty BLOB Azure** nebo **soubory Azure**.
  > [!NOTE]
  > Aplikace kontejneru Windows podporují pouze soubory Azure.
- **Kontejner úložiště**: Pro základní konfiguraci kontejneru, který chcete.
- **Název sdílené složky**: V případě pokročilé konfigurace soubor název sdílené složky.
- **Přístupový klíč**: V případě pokročilé konfigurace, přístupový klíč.
- **Cesta připojení**: Absolutní cesta ve vašem kontejneru připojit vlastní úložiště.

Další informace najdete v tématu [poskytování obsahu z Azure Storage ve službě App Service v Linuxu](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurovat nastavení jazyka zásobníku

Pro Linuxové aplikace naleznete v tématu:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurace vlastní kontejnery

Zobrazit [nakonfigurovat vlastní kontejner Linuxu pro službu Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Další postup

- [Konfigurace vlastní domény ve službě Azure App Service]
- [Nastavení přípravných prostředí ve službě Azure App Service]
- [Povolení HTTPS pro aplikaci ve službě Azure App Service]
- [Povolení diagnostických protokolů](troubleshoot-diagnostic-logs.md)
- [Škálování aplikace ve službě Azure App Service]
- [Monitorování základy ve službě Azure App Service]
- [Změnit nastavení applicationHost.config s applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[Funkce SignalR technologie ASP.NET]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurace vlastní domény ve službě Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Nastavení přípravných prostředí ve službě Azure App Service]: ./deploy-staging-slots.md
[Povolení HTTPS pro aplikaci ve službě Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Monitorování základy ve službě Azure App Service]: ./web-sites-monitor.md
[režim kanálů]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Škálování aplikace ve službě Azure App Service]: ./web-sites-scale.md
