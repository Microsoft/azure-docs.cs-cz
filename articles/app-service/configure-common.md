---
title: Konfigurace aplikací na portálu
description: Naučte se nakonfigurovat běžná nastavení aplikace App Service na webu Azure Portal. Nastavení aplikací, připojovací řetězce, platforma, zásobník jazyka, kontejner atd.
keywords: azure app service, webová aplikace, nastavení aplikací, proměnné prostředí
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: ce0a170a629f347e2687a2e9f63fb3438fe2bd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280167"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurace aplikace Služby app service na webu Azure Portal

Toto téma vysvětluje, jak nakonfigurovat běžná nastavení pro webové aplikace, mobilní back-end nebo aplikaci API pomocí [portálu Azure .]

## <a name="configure-app-settings"></a>Konfigurace nastavení aplikace

Ve službě App Service jsou nastavení aplikace proměnné předané jako proměnné prostředí do kódu aplikace. Pro linuxové aplikace a vlastní kontejnery, App `--env` Service předá nastavení aplikace do kontejneru pomocí příznaku nastavit proměnnou prostředí v kontejneru.

Na [webu Azure Portal]vyhledejte a vyberte **Služby aplikací**a vyberte aplikaci. 

![Hledání služeb aplikace](./media/configure-common/search-for-app-services.png)

V levé nabídce aplikace vyberte **Nastavení konfigurační** > **aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

Pro vývojáře ASP.NET a ASP.NET Core je nastavení nastavení `<appSettings>` aplikací ve službě App Service jako jejich nastavení v *souboru Web.config* nebo *appsettings.json*, ale hodnoty ve službě App Service přepisují hodnoty v *souboru Web.config* nebo *appsettings.json*. Nastavení vývoje (například místní heslo MySQL) můžete uchovávat v *souboru Web.config* nebo *appsettings.json*, ale v app service jsou v bezpečí produkční tajné kódy (například heslo databáze Azure MySQL). Stejný kód používá nastavení vývoje při ladění místně a používá vaše produkční tajné kódy při nasazení do Azure.

Ostatní zásobníky jazyků, podobně získat nastavení aplikace jako proměnné prostředí za běhu. Konkrétní kroky pro stoh jazyka najdete v následujících tématech:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](containers/configure-custom-container.md#configure-environment-variables)

Nastavení aplikací jsou při uložení vždy šifrována (šifrovaná v klidovém stavu).

> [!NOTE]
> Nastavení aplikace lze také přeložit z [trezoru klíčů](/azure/key-vault/) pomocí [odkazů trezoru klíčů](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Zobrazit skryté hodnoty

Ve výchozím nastavení jsou hodnoty pro nastavení aplikace skryté na portálu pro zabezpečení. Chcete-li zobrazit skrytou hodnotu nastavení aplikace, klepněte na pole **Hodnota** tohoto nastavení. Pokud chcete zobrazit hodnoty všech nastavení aplikace, klikněte na tlačítko **Zobrazit hodnotu.**

### <a name="add-or-edit"></a>Přidání nebo úprava

Chcete-li přidat nové nastavení aplikace, klepněte na tlačítko **Nové nastavení aplikace**. V dialogovém okně můžete [přilepit nastavení k aktuálnímu slotu](deploy-staging-slots.md#which-settings-are-swapped).

Chcete-li nastavení upravit, klepněte na tlačítko **Upravit** na pravé straně.

Po dokončení klepněte na tlačítko **Aktualizovat**. Nezapomeňte kliknout na **tlačítko Uložit** zpět na stránce **Konfigurace.**

> [!NOTE]
> Ve výchozím kontejneru Linuxu nebo ve vlastním kontejneru Linuxu musí být `ApplicationInsights:InstrumentationKey` libovolná vnořená `ApplicationInsights__InstrumentationKey` struktura klíčů JSON v názvu nastavení aplikace, jako je, nakonfigurována ve službě App Service jako pro název klíče. Jinými slovy, `:` všechny by `__` měly být nahrazeny (dvojité podtržítko).
>

### <a name="edit-in-bulk"></a>Hromadné úpravy

Pokud chcete hromadně přidávat nebo upravovat nastavení aplikace, klikněte na tlačítko **Upřesnit úpravy.** Po dokončení klepněte na tlačítko **Aktualizovat**. Nezapomeňte kliknout na **tlačítko Uložit** zpět na stránce **Konfigurace.**

Nastavení aplikace mají následující formátování JSON:

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

Na [webu Azure Portal]vyhledejte a vyberte **Služby aplikací**a vyberte aplikaci. V levé nabídce aplikace vyberte **Nastavení konfigurační** > **aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

Pro vývojáře ASP.NET a ASP.NET Core je nastavení připojovacích řetězců ve službě App Service stejné jako jejich nastavení v `<connectionStrings>` *souboru Web.config*, ale hodnoty nastavené ve službě App Service přepisují hodnoty v *souboru Web.config*. Nastavení vývoje (například databázový soubor) můžete bezpečně uchovávat v tajných *kódech Web.config* a produkčních kódech (například pověření databáze SQL) bezpečně ve službě App Service. Stejný kód používá nastavení vývoje při ladění místně a používá vaše produkční tajné kódy při nasazení do Azure.

Pro ostatní jazykové zásobníky je lepší použít [nastavení aplikace,](#configure-app-settings) protože připojovací řetězce vyžadují speciální formátování v klávesách proměnných pro přístup k hodnotám. Tady je však jedna výjimka: některé typy databází Azure jsou zálohovány spolu s aplikací, pokud nakonfigurujete jejich připojovací řetězce ve vaší aplikaci. Další informace naleznete v tématu [Co se zálohuje](manage-backup.md#what-gets-backed-up). Pokud tuto automatickou zálohu nepotřebujete, použijte nastavení aplikace.

Za běhu jsou připojovací řetězce k dispozici jako proměnné prostředí s předponou s následujícími typy připojení:

* SQL Server:`SQLCONNSTR_`
* Mysql:`MYSQLCONNSTR_`
* Databáze SQL:`SQLAZURECONNSTR_`
* Vlastní:`CUSTOMCONNSTR_`

Například připojovací řetězec MySql s názvem *Connectionstring1* lze přistupovat jako proměnná `MYSQLCONNSTR_connectionString1`prostředí . Konkrétní kroky pro stoh jazyka najdete v následujících tématech:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](containers/configure-custom-container.md#configure-environment-variables)

Připojovací řetězce jsou při uložení vždy zašifrovány (šifrované v klidovém stavu).

> [!NOTE]
> Připojovací řetězce lze také přeložit z [trezoru klíčů](/azure/key-vault/) pomocí [odkazů trezoru klíčů](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Zobrazit skryté hodnoty

Ve výchozím nastavení jsou hodnoty připojovacích řetězců skryty na portálu pro zabezpečení. Chcete-li zobrazit skrytou hodnotu připojovacího řetězce, stačí kliknout na pole **Hodnota** tohoto řetězce. Chcete-li zobrazit hodnoty všech připojovacích řetězců, klepněte na tlačítko **Zobrazit hodnotu.**

### <a name="add-or-edit"></a>Přidání nebo úprava

Chcete-li přidat nový připojovací řetězec, klepněte na tlačítko **Nový připojovací řetězec**. V dialogovém okně můžete [připojovací řetězec přilepit k aktuální patici](deploy-staging-slots.md#which-settings-are-swapped).

Chcete-li nastavení upravit, klepněte na tlačítko **Upravit** na pravé straně.

Po dokončení klepněte na tlačítko **Aktualizovat**. Nezapomeňte kliknout na **tlačítko Uložit** zpět na stránce **Konfigurace.**

### <a name="edit-in-bulk"></a>Hromadné úpravy

Chcete-li přidat nebo upravit připojovací řetězce hromadně, klepněte na tlačítko **Upřesnit úpravy.** Po dokončení klepněte na tlačítko **Aktualizovat**. Nezapomeňte kliknout na **tlačítko Uložit** zpět na stránce **Konfigurace.**

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

Na [webu Azure Portal]vyhledejte a vyberte **Služby aplikací**a vyberte aplikaci. V levé nabídce aplikace vyberte**Nastavení obecné** **konfigurace** > .

![Obecná nastavení](./media/configure-common/open-general.png)

Zde můžete nakonfigurovat některá běžná nastavení aplikace. Některá nastavení [vyžadují, abyste vertikálně navýšili kapacitu na vyšší cenové úrovně](manage-scale-up.md).

- **Nastavení zásobníku**: Zásobník softwaru pro spuštění aplikace, včetně jazykových verzí a verzí sady SDK. Pro linuxové aplikace a vlastní kontejnerové aplikace můžete také nastavit volitelný příkaz nebo soubor pro spuštění.
- **Nastavení platformy**: Umožňuje konfigurovat nastavení pro hostitelskou platformu, včetně:
    - **Bitness**: 32bitová nebo 64bitová.
    - **WebSocket protokol**: Pro [ASP.NET SignalR] nebo [socket.io](https://socket.io/), například.
    - **Vždy zapnuto**: Udržujte aplikaci načtenou, i když není žádný provoz. Je vyžadována pro průběžné webové úlohy nebo pro webjobs, které jsou spouštěny pomocí výrazu CRON.
      > [!NOTE]
      > Pomocí funkce Vždy zapnuto nemůžete řídit koncový bod. Vždy odešle požadavek do kořenového adresáře aplikace.
    - **Verze spravovaného kanálu**: [Režim kanálu iis]. Pokud máte starší verzi aplikace, která vyžaduje starší verzi iis, nastavte ji na **klasickou.**
    - **Http verze**: Chcete-li povolit podporu protokolu [HTTPS/2,](https://wikipedia.org/wiki/HTTP/2) nastavte na **2.0.**
    > [!NOTE]
    > Většina moderních prohlížečů podporuje protokol HTTP/2 pouze přes TLS, zatímco nešifrovaný provoz nadále používá HTTP/1.1. Chcete-li zajistit, aby se klientské prohlížeče připojovaly k vaší aplikaci pomocí protokolu HTTP/2, [zabezpečte vlastní název DNS vazbou SSL ve službě Azure App Service](configure-ssl-bindings.md).
    - **ARR spřažení**: V nasazení s více instancemi, ujistěte se, že klient je směrován do stejné instance po dobu životnosti relace. Tuto možnost můžete nastavit na **vypnuto** pro bezstavové aplikace.
- **Ladění**: Povolit vzdálené ladění pro [aplikace ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), ASP.NET [Core](/visualstudio/debugger/remote-debugging-azure)nebo [Node.js.](containers/configure-language-nodejs.md#debug-remotely) Tato možnost se automaticky vypne po 48 hodinách.
- **Příchozí klientské certifikáty**: vyžadují klientské certifikáty při [vzájemném ověřování](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurace výchozích dokumentů

Toto nastavení platí pouze pro aplikace pro Windows.

Na [webu Azure Portal]vyhledejte a vyberte **Služby aplikací**a vyberte aplikaci. V levé nabídce aplikace vyberte**Výchozí dokumenty** **konfigurace** > .

![Výchozí dokumenty](./media/configure-common/open-documents.png)

Výchozí dokument je webová stránka, která se zobrazí na kořenové adrese URL webu. Použije se první odpovídající soubor v seznamu. Chcete-li přidat nový výchozí dokument, klepněte na **tlačítko Nový dokument**. Nezapomeňte **kliknout**na uložit .

Pokud aplikace používá moduly, které směrují na základě adresy URL namísto zobrazování statického obsahu, není potřeba výchozí chdokumentů.

## <a name="configure-path-mappings"></a>Konfigurace mapování cest

Na [webu Azure Portal]vyhledejte a vyberte **Služby aplikací**a vyberte aplikaci. V levé nabídce aplikace vyberte**mapování cesty** **konfigurace** > .

![Mapování cesty](./media/configure-common/open-path.png)

Stránka **Mapování cesty** zobrazuje různé věci na základě typu operačního systému.

### <a name="windows-apps-uncontainerized"></a>Aplikace pro Windows (bez kontejneru)

Pro aplikace pro Windows můžete přizpůsobit mapování obslužné rutiny služby IIS a virtuální aplikace a adresáře.

Mapování obslužných rutin umožňuje přidávat vlastní skriptové procesory pro zpracování požadavků na konkrétní přípony souborů. Chcete-li přidat vlastní obslužnou rutinu, klepněte na tlačítko **Nová obslužná rutina**. Nakonfigurujte obslužnou rutinu následujícím způsobem:

- **Rozšíření**. Přípona souboru, který chcete zpracovat, například * \*.php* nebo *handler.fcgi*.
- **Skriptovací procesor**. Absolutní cesta skriptového procesoru k vám. Požadavky na soubory, které odpovídají příponě souboru, jsou zpracovány skriptovacím procesorem. Pomocí cesty `D:\home\site\wwwroot` můžete odkazovat na kořenový adresář aplikace.
- **Argumenty**. Volitelné argumenty příkazového řádku pro skriptovací procesor.

Každá aplikace má výchozí`/`kořenovou cestu `D:\home\site\wwwroot`( ) namapovanou na , kde je váš kód nasazen ve výchozím nastavení. Pokud je kořen aplikace v jiné složce nebo pokud vaše úložiště obsahuje více než jednu aplikaci, můžete zde upravit nebo přidat virtuální aplikace a adresáře. Klepněte na **položku Nová virtuální aplikace nebo adresář**.

Chcete-li konfigurovat virtuální aplikace a adresáře, zadejte každý virtuální`D:\home`adresář a jeho odpovídající fyzickou cestu vzhledem ke kořenovému adresáři webu ( ). Volitelně můžete zaškrtnout **políčko Aplikace** a označit virtuální adresář jako aplikaci.

### <a name="containerized-apps"></a>Kontejnerizované aplikace

Pro [kontejnerizovanou aplikaci](containers/how-to-serve-content-from-azure-storage.md)můžete přidat vlastní úložiště . Kontejnerizované aplikace zahrnují všechny aplikace pro Linux a také vlastní kontejnery pro Windows a Linux spuštěné ve službě App Service. Klikněte na **Nový držák úložiště Azure** a nakonfigurujte vlastní úložiště takto:

- **Název**: Zobrazovaný název.
- **Možnosti konfigurace**: **Základní** nebo **Upřesnit .**
- **Účty úložiště**: Účet úložiště s požadovaným kontejnerem.
- **Typ úložiště:** **Objekty BLOB Azure** nebo **soubory Azure**.
  > [!NOTE]
  > Aplikace kontejneru Windows podporují jenom soubory Azure.
- **Kontejner úložiště**: Pro základní konfiguraci, kontejner, který chcete.
- **Název sdílené položky**: Pro pokročilou konfiguraci název sdílené složky.
- **Přístupový klíč**: Pro pokročilou konfiguraci přístupový klíč.
- **Cesta k připojení**: Absolutní cesta v kontejneru pro připojení vlastního úložiště.

Další informace najdete v článku [Obsluha obsahu z Azure Storage v App Service na Linuxu](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurace nastavení zásobníku jazyků

Informace o aplikacích pro Linux najdete v tématu:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurace vlastních kontejnerů

Viz [Konfigurace vlastního kontejneru Linuxu pro Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Další kroky

- [Konfigurace vlastního názvu domény ve službě Azure App Service]
- [Nastavení přípravných prostředí ve službě Azure App Service]
- [Zabezpečení vlastního názvu DNS s využitím vazby SSL ve službě Azure App Service](configure-ssl-bindings.md)
- [Povolení diagnostických protokolů](troubleshoot-diagnostic-logs.md)
- [Škálování aplikace ve službě Azure App Service]
- [Monitorování základních škol ve službě Azure App Service]
- [Změna nastavení applicationHost.config pomocí applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurace vlastního názvu domény ve službě Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Nastavení přípravných prostředí ve službě Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Monitorování základních škol ve službě Azure App Service]: ./web-sites-monitor.md
[režim potrubí]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Škálování aplikace ve službě Azure App Service]: ./manage-scale-up.md
