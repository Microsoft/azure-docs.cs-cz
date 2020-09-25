---
title: Konfigurace aplikací na portálu
description: Naučte se konfigurovat společná nastavení pro App Service aplikaci v Azure Portal. Nastavení aplikace, připojovací řetězce, platforma, sada jazyků, kontejner atd.
keywords: Azure App Service, Webová aplikace, nastavení aplikace, proměnné prostředí
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 18463c4350895401c9bf73dc249ce93218a44f7c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264639"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurace aplikace App Service v Azure Portal

Toto téma vysvětluje, jak nakonfigurovat společná nastavení pro webové aplikace, mobilní back-end nebo aplikaci API pomocí [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurace nastavení aplikace

V App Service jsou nastavení aplikace proměnné předány jako proměnné prostředí do kódu aplikace. U aplikací pro Linux a vlastních kontejnerů App Service předá nastavení aplikace kontejneru pomocí `--env` příznaku, který nastaví proměnnou prostředí v kontejneru.

V [Azure Portal]vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci. 

![Hledat App Services](./media/configure-common/search-for-app-services.png)

V nabídce vlevo aplikace vyberte nastavení **Konfigurace**  >  **aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

V případě vývojářů ASP.NET a ASP.NET Core je nastavení aplikace v App Service třeba nastavit v `<appSettings>` v *Web.config* nebo *appsettings.jsna*, ale hodnoty v App Service přepisují ty *Web.config* nebo *appsettings.jsna*. Můžete zachovat nastavení pro vývoj (například místní heslo MySQL) ve *Web.config* nebo *appsettings.jsna*, ale provozní tajemství (například heslo databáze MySQL Azure) jsou v App Service bezpečná. Stejný kód používá vaše vývojové nastavení při ladění místně a při nasazení do Azure používá vaše provozní tajemství.

Další jazykové zásobníky také získají nastavení aplikace jako proměnné prostředí za běhu. Postup pro konkrétní jazykový zásobník najdete v těchto tématech:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](configure-custom-container.md#configure-environment-variables)

Nastavení aplikace jsou při ukládání vždy zašifrovaná (zašifrovaná po klidové době).

> [!NOTE]
> Nastavení aplikace je také možné vyřešit z [Key Vault](../key-vault/index.yml) pomocí [Key Vaultch odkazů](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Zobrazit skryté hodnoty

Ve výchozím nastavení jsou hodnoty nastavení aplikace na portálu pro zabezpečení skryté. Chcete-li zobrazit skrytou hodnotu nastavení aplikace, klikněte na pole **hodnota** daného nastavení. Pokud chcete zobrazit hodnoty všech nastavení aplikace, klikněte na tlačítko **Zobrazit hodnotu** .

### <a name="add-or-edit"></a>Přidat nebo upravit

Chcete-li přidat nové nastavení aplikace, klikněte na tlačítko **nové nastavení aplikace**. V dialogovém okně můžete [nastavení nastavit na aktuální slot](deploy-staging-slots.md#which-settings-are-swapped).

Chcete-li upravit nastavení, klikněte na tlačítko **Upravit** na pravé straně.

Po dokončení klikněte na **aktualizovat**. Nezapomeňte kliknout na **Uložit** zpátky na stránce **Konfigurace** .

> [!NOTE]
> Ve výchozím kontejneru Linux nebo vlastním kontejneru Linux jsou všechny vnořené struktury klíčů JSON v názvu nastavení aplikace, jako je `ApplicationInsights:InstrumentationKey` třeba nakonfigurovat v App Service jako `ApplicationInsights__InstrumentationKey` název klíče. Jinými slovy, libovolná `:` by měla být nahrazena `__` (dvojitým podtržítkem).
>

### <a name="edit-in-bulk"></a>Hromadná úprava

Chcete-li přidat nebo upravit nastavení aplikace hromadně, klikněte na tlačítko **Rozšířené úpravy** . Po dokončení klikněte na **aktualizovat**. Nezapomeňte kliknout na **Uložit** zpátky na stránce **Konfigurace** .

Nastavení aplikace má následující formátování JSON:

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

V [Azure Portal]vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci. V nabídce vlevo aplikace vyberte nastavení **Konfigurace**  >  **aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

V případě vývojářů ASP.NET a ASP.NET Core nastavení připojovacích řetězců v App Service je třeba je nastavovat v `<connectionStrings>` v *Web.config*, ale hodnoty, které nastavíte App Service přepisují ty v *Web.config*. Nastavení vývoje (například databázový soubor) můžete zachovat v *Web.config* a v produkčních tajných klíčích (například SQL Database pověření) v App Service. Stejný kód používá vaše vývojové nastavení při ladění místně a při nasazení do Azure používá vaše provozní tajemství.

Pro jiné jazykové zásobníky je vhodnější místo toho použít [nastavení aplikace](#configure-app-settings) , protože připojovací řetězce vyžadují pro přístup k hodnotám speciální formátování v proměnných klíčů. Tady je jedna výjimka. některé typy databází Azure se ale zálohují společně s aplikací, pokud ve své aplikaci nakonfigurujete své připojovací řetězce. Další informace najdete v tématu [co se zálohuje](manage-backup.md#what-gets-backed-up). Pokud tuto automatizovanou zálohu nepotřebujete, použijte nastavení aplikace.

V době běhu jsou připojovací řetězce k dispozici jako proměnné prostředí s předponou následujících typů připojení:

* Hostitel `SQLCONNSTR_`  
* MySQL `MYSQLCONNSTR_` 
* SQLAzure: `SQLAZURECONNSTR_` 
* Uživatelská `CUSTOMCONNSTR_`
* PostgreSQL `POSTGRESQLCONNSTR_`  

Například připojovací řetězec MySql s názvem *ConnectionString1* je k dispozici jako proměnná prostředí `MYSQLCONNSTR_connectionString1` . Postup pro konkrétní jazykový zásobník najdete v těchto tématech:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](configure-custom-container.md#configure-environment-variables)

Připojovací řetězce jsou při uložení vždy zašifrované (zašifrované – při REST).

> [!NOTE]
> Připojovací řetězce je také možné přeložit z [Key Vault](../key-vault/index.yml) pomocí [Key Vaultch odkazů](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Zobrazit skryté hodnoty

Ve výchozím nastavení jsou hodnoty připojovacích řetězců na portálu skryty pro zabezpečení. Chcete-li zobrazit skrytou hodnotu připojovacího řetězce, stačí kliknout na pole **hodnota** daného řetězce. Chcete-li zobrazit hodnoty všech připojovacích řetězců, klikněte na tlačítko **Zobrazit hodnotu** .

### <a name="add-or-edit"></a>Přidat nebo upravit

Chcete-li přidat nový připojovací řetězec, klikněte na **nový připojovací řetězec**. V dialogovém okně můžete [připojovací řetězec nalepit na aktuální slot](deploy-staging-slots.md#which-settings-are-swapped).

Chcete-li upravit nastavení, klikněte na tlačítko **Upravit** na pravé straně.

Po dokončení klikněte na **aktualizovat**. Nezapomeňte kliknout na **Uložit** zpátky na stránce **Konfigurace** .

### <a name="edit-in-bulk"></a>Hromadná úprava

Chcete-li přidat nebo upravit připojovací řetězce hromadně, klikněte na tlačítko **Rozšířené úpravy** . Po dokončení klikněte na **aktualizovat**. Nezapomeňte kliknout na **Uložit** zpátky na stránce **Konfigurace** .

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

V [Azure Portal]vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci. V nabídce vlevo aplikace vyberte **Konfigurace**  >  **Obecné nastavení**.

![Obecná nastavení](./media/configure-common/open-general.png)

Tady můžete nakonfigurovat některá společná nastavení aplikace. Některá nastavení vyžadují [horizontální navýšení kapacity až na vyšší cenové úrovně](manage-scale-up.md).

- **Nastavení zásobníku**: softwarový zásobník pro spuštění aplikace, včetně verze jazyka a sady SDK. Pro aplikace pro Linux a vlastní kontejnerové aplikace můžete také nastavit volitelný spouštěcí příkaz nebo soubor.
- **Nastavení platformy**: umožňuje konfigurovat nastavení pro hostující platformu, včetně:
    - **Bitová verze**: 32-bit nebo 64-bit.
    - **Protokol WebSocket**: pro [ASP.NET signál] nebo [Socket.IO](https://socket.io/), například.
    - **Always On**: aplikace zachovává načtení i v případě, že není k dispozici žádný provoz. Vyžaduje se pro nepřetržité webové úlohy nebo pro WebJobs, které se spouštějí pomocí výrazu CRON.
      > [!NOTE]
      > Při použití funkce Always On pošle front-end vyrovnávání zatížení žádost do kořenového adresáře aplikace. Tento koncový bod aplikace App Service nelze nakonfigurovat.
    - **Spravovaná verze kanálu**: [režim kanálu]IIS. Pokud máte starší verzi aplikace, která vyžaduje starší verzi služby IIS, nastavte ji na **klasický** .
    - **Verze protokolu HTTP**: nastavte na **2,0** pro povolení podpory protokolu [https/2](https://wikipedia.org/wiki/HTTP/2) .
    > [!NOTE]
    > Většina moderních prohlížečů podporuje protokol HTTP/2 jenom přes TLS, zatímco nešifrovaný provoz dál používá protokol HTTP/1.1. Pokud chcete zajistit, aby se prohlížeče klienta připojovaly k vaší aplikaci pomocí protokolu HTTP/2, zabezpečte si vlastní název DNS. Další informace najdete v tématu [zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md).
    - **Spřažení ARR**: v nasazení s více instancemi zajistěte, aby byl klient směrován do stejné instance po dobu životnosti relace. Tuto možnost můžete nastavit na **vypnuto** u bezstavových aplikací.
- **Ladění**: Povolte vzdálené ladění pro aplikace [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)nebo [Node.js](configure-language-nodejs.md#debug-remotely) . Tato možnost se automaticky vypne po 48 hodinách.
- **Příchozí klientské certifikáty**: vyžadují klientské certifikáty při [vzájemném ověřování](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurovat výchozí dokumenty

Toto nastavení platí jenom pro aplikace pro Windows.

V [Azure Portal]vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci. V nabídce vlevo aplikace vyberte **Konfigurace**  >  **výchozí dokumenty**.

![Výchozí dokumenty](./media/configure-common/open-documents.png)

Výchozí dokument je webová stránka, která se zobrazuje na kořenové adrese URL webu. Použije se první vyhovující soubor v seznamu. Chcete-li přidat nový výchozí dokument, klikněte na tlačítko **Nový dokument**. Nezapomeňte kliknout na **Uložit**.

Pokud aplikace používá moduly, které směrují na základě adresy URL namísto obsluhy statického obsahu, nemusíte používat výchozí dokumenty.

## <a name="configure-path-mappings"></a>Konfigurace mapování cest

V [Azure Portal]vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci. V nabídce vlevo aplikace vyberte **Configuration**  >  **mapování cest**konfigurace.

![Mapování cest](./media/configure-common/open-path.png)

Na stránce **mapování cest** se zobrazují různé věci podle typu operačního systému.

### <a name="windows-apps-uncontainerized"></a>Aplikace pro Windows (nekontejnerované)

Pro aplikace pro Windows můžete přizpůsobit mapování obslužných rutin služby IIS a virtuální aplikace a adresáře.

Mapování obslužných rutin umožňují přidat vlastní skriptové procesory, které budou zpracovávat požadavky na konkrétní přípony souborů. Chcete-li přidat vlastní obslužnou rutinu, klikněte na tlačítko **Nová obslužná rutina**. Nastavte obslužnou rutinu následujícím způsobem:

- **Přípona**. Přípona souboru, kterou chcete zpracovat, například * \* . php* nebo *obslužných rutin. fcgi*.
- **Procesor skriptů**. Absolutní cesta k procesoru skriptu. Požadavky na soubory, které odpovídají příponám souborů, zpracovává procesor skriptu. Použijte cestu `D:\home\site\wwwroot` pro odkaz na kořenový adresář vaší aplikace.
- **Argumenty**. Volitelné argumenty příkazového řádku pro procesor skriptu.

Každá aplikace má výchozí kořenovou cestu ( `/` ) namapovanou na `D:\home\site\wwwroot` , kde je váš kód nasazený ve výchozím nastavení. Pokud je kořenový adresář aplikace v jiné složce nebo pokud vaše úložiště obsahuje více než jednu aplikaci, můžete zde upravit nebo přidat virtuální aplikace a adresáře. Klikněte na **Nová virtuální aplikace nebo adresář**.

Chcete-li konfigurovat virtuální aplikace a adresáře, zadejte každý virtuální adresář a jeho odpovídající fyzickou cestu vzhledem k kořenovému adresáři webu ( `D:\home` ). Volitelně můžete zaškrtnout políčko **aplikace** a označit tak virtuální adresář jako aplikaci.

### <a name="containerized-apps"></a>Kontejnerové aplikace

Můžete [Přidat vlastní úložiště pro svou kontejnerovou aplikaci](configure-connect-to-azure-storage.md). Kontejnerové aplikace zahrnují všechny aplikace pro Linux a také vlastní kontejnery pro Windows a Linux běžící na App Service. Klikněte na **nový Azure Storage připojit** a nakonfigurujte vlastní úložiště následujícím způsobem:

- **Název**: zobrazovaný název.
- **Možnosti konfigurace**: **základní** nebo **Rozšířené**.
- **Účty úložiště**: účet úložiště požadovaného kontejneru.
- **Typ úložiště**: **objekty blob Azure** nebo **soubory Azure**.
  > [!NOTE]
  > Aplikace služby Windows Container podporují pouze soubory Azure.
- **Kontejner úložiště**: pro základní konfiguraci, kontejner, který chcete.
- **Název sdílené složky**: pro pokročilou konfiguraci, název sdílené složky.
- **Přístupový klíč**: pro pokročilou konfiguraci se jedná o přístupový klíč.
- **Cesta pro připojení**: absolutní cesta ve vašem kontejneru pro připojení vlastního úložiště.

Další informace najdete v tématu věnovaném [přístupu Azure Storage jako sdílené síťové složky z kontejneru v App Service](configure-connect-to-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurovat nastavení zásobníku jazyka

Pro aplikace pro Linux se podívejte na:

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurace vlastních kontejnerů

Další informace najdete v tématu [Konfigurace vlastního kontejneru Linux pro Azure App Service](configure-custom-container.md) .

## <a name="next-steps"></a>Další kroky

- [Konfigurace vlastního názvu domény v Azure App Service]
- [Nastavení přípravných prostředí ve službě Azure App Service]
- [Zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md)
- [Povolení diagnostických protokolů](troubleshoot-diagnostic-logs.md)
- [Horizontální navýšení kapacity aplikace v Azure App Service]
- [Základy monitorování v Azure App Service]
- [Změna nastavení applicationHost.config pomocí souboru applicationHost. xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurace vlastního názvu domény v Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Nastavení přípravných prostředí ve službě Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Základy monitorování v Azure App Service]: ./web-sites-monitor.md
[režim kanálů]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Horizontální navýšení kapacity aplikace v Azure App Service]: ./manage-scale-up.md