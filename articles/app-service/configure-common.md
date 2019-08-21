---
title: Konfigurace aplikací na portálu – Azure App Service
description: Naučte se konfigurovat společná nastavení pro App Service aplikaci v Azure Portal.
keywords: Azure App Service, Webová aplikace, nastavení aplikace, proměnné prostředí
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9609a886bdcbf093cffaf888f9aeaff0d61acec1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636648"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurace aplikace App Service v Azure Portal

Toto téma vysvětluje, jak nakonfigurovat společná nastavení pro webové aplikace, mobilní back-end nebo aplikaci API pomocí [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurace nastavení aplikace

V App Service jsou nastavení aplikace proměnné předány jako proměnné prostředí do kódu aplikace. U aplikací pro Linux a vlastních kontejnerů App Service předá nastavení aplikace kontejneru pomocí `--env` příznaku, který nastaví proměnnou prostředí v kontejneru.

V [Azure Portal]přejděte na stránku správy vaší aplikace. V nabídce vlevo aplikace klikněte na nastavení **Konfigurace** > **aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

Pro vývojáře v ASP.NET a ASP.NET Core je nastavení aplikace v App Service třeba `<appSettings>` nastavit v *souboru Web. config* nebo *appSettings. JSON*, ale hodnoty v App Service přepisují ty v *souboru Web. config* nebo  *appSettings. JSON*. Můžete zachovat nastavení pro vývoj (například místní heslo MySQL) v souboru *Web. config* nebo *appSettings. JSON*, ale v App Service jsou bezpečné provozní tajné klíče (například heslo databáze Azure MySQL). Stejný kód používá vaše vývojové nastavení při ladění místně a při nasazení do Azure používá vaše provozní tajemství.

Další jazykové zásobníky také získají nastavení aplikace jako proměnné prostředí za běhu. Postup pro konkrétní jazykový zásobník najdete v těchto tématech:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](containers/configure-custom-container.md#configure-environment-variables)

Nastavení aplikace jsou při ukládání vždy zašifrovaná (zašifrovaná po klidové době).

> [!NOTE]
> Nastavení aplikace je také možné vyřešit z [Key Vault](/azure/key-vault/) pomocí [Key Vaultch odkazů](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Zobrazit skryté hodnoty

Ve výchozím nastavení jsou hodnoty nastavení aplikace na portálu pro zabezpečení skryté. Chcete-li zobrazit skrytou hodnotu nastavení aplikace, klikněte na pole **hodnota** daného nastavení. Pokud chcete zobrazit hodnoty všech nastavení aplikace, klikněte na tlačítko **Zobrazit hodnotu** .

### <a name="add-or-edit"></a>Přidat nebo upravit

Chcete-li přidat nové nastavení aplikace, klikněte na tlačítko **nové nastavení aplikace**. V dialogovém okně můžete [nastavení nastavit na aktuální slot](deploy-staging-slots.md#which-settings-are-swapped).

Chcete-li upravit nastavení, klikněte na tlačítko **Upravit** na pravé straně.

Po dokončení klikněte na **aktualizovat**. Nezapomeňte kliknout na **Uložit** zpátky na stránce **Konfigurace** .

> [!NOTE]
> Ve výchozím kontejneru Linux nebo vlastním kontejneru Linux jsou všechny vnořené struktury klíčů JSON v názvu `ApplicationInsights:InstrumentationKey` nastavení aplikace, jako je třeba nakonfigurovat v App Service jako `ApplicationInsights__InstrumentationKey` název klíče. Jinými slovy, libovolná `:` by měla být `__` nahrazena (dvojitým podtržítkem).
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

V [Azure Portal]přejděte na stránku správy aplikace. V nabídce vlevo aplikace klikněte na nastavení **Konfigurace** > **aplikace**.

![Nastavení aplikace](./media/configure-common/open-ui.png)

V případě vývojářů ASP.NET a ASP.NET Core je nastavení připojovacích řetězců v App Service jako nastavení `<connectionStrings>` v *souboru Web. config*, ale hodnoty, které jste nastavili App Service přepisují v *souboru Web. config*. V App Service můžete zachovat nastavení pro vývoj (například databázový soubor) v souboru *Web. config* a v produkčních tajných klíčích (například SQL Database pověření). Stejný kód používá vaše vývojové nastavení při ladění místně a při nasazení do Azure používá vaše provozní tajemství.

Pro jiné jazykové zásobníky je vhodnější místo toho použít [nastavení aplikace](#configure-app-settings) , protože připojovací řetězce vyžadují pro přístup k hodnotám speciální formátování v proměnných klíčů. Tady je jedna výjimka. některé typy databází Azure se ale zálohují společně s aplikací, pokud ve své aplikaci nakonfigurujete své připojovací řetězce. Další informace najdete v tématu [co](manage-backup.md#what-gets-backed-up)se zálohuje. Pokud tuto automatizovanou zálohu nepotřebujete, použijte nastavení aplikace.

V době běhu jsou připojovací řetězce k dispozici jako proměnné prostředí s předponou následujících typů připojení:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database:`SQLAZURECONNSTR_`
* Uživatelská`CUSTOMCONNSTR_`

Například připojovací řetězec MySql s názvem *ConnectionString1* je k dispozici jako proměnná `MYSQLCONNSTR_connectionString1`prostředí. Postup pro konkrétní jazykový zásobník najdete v těchto tématech:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Vlastní kontejnery](containers/configure-custom-container.md#configure-environment-variables)

Připojovací řetězce jsou při uložení vždy zašifrované (zašifrované – při REST).

> [!NOTE]
> Připojovací řetězce je také možné přeložit z [Key Vault](/azure/key-vault/) pomocí [Key Vaultch odkazů](app-service-key-vault-references.md).

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

V [Azure Portal]přejděte na stránku správy aplikace. V nabídce vlevo aplikace klikněte na nastavení **Konfigurace** > **aplikace**.

![Obecná nastavení](./media/configure-common/open-general.png)

Tady můžete nakonfigurovat některá společná nastavení aplikace. Některá nastavení vyžadují [horizontální navýšení kapacity až na vyšší cenové úrovně](manage-scale-up.md).

- **Nastavení zásobníku**: Softwarový zásobník, ve kterém se aplikace spustí, včetně jazyka a verze sady SDK. Pro aplikace pro Linux a vlastní kontejnerové aplikace můžete také nastavit volitelný spouštěcí příkaz nebo soubor.
- **Nastavení platformy**: Umožňuje konfigurovat nastavení pro hostující platformu, včetně:
    - **Bitová verze**: 32-bit nebo 64-bit.
    - **Protokol WebSocket**: Například pro [ASP.NET signál] nebo [Socket.IO](https://socket.io/).
    - **Vždy zapnuto**: Nechte aplikaci zavedenou i v případě, že nedochází k žádným přenosům. Vyžaduje se pro nepřetržité webové úlohy nebo pro WebJobs, které se spouštějí pomocí výrazu CRON.
    - **Verze spravovaného kanálu**: [Režim kanálu]IIS. Pokud máte starší verzi aplikace, která vyžaduje starší verzi služby IIS, nastavte ji na **klasický** .
    - **Verze protokolu HTTP**: Nastavte na **2,0** pro povolení podpory protokolu [https/2](https://wikipedia.org/wiki/HTTP/2) .
    > [!NOTE]
    > Většina moderních prohlížečů podporuje protokol HTTP/2 jenom přes TLS, zatímco nešifrovaný provoz dál používá protokol HTTP/1.1. Pokud chcete zajistit, aby se prohlížeče klienta připojovaly k vaší aplikaci pomocí protokolu HTTP/2, [Kupte si App Service Certificate](web-sites-purchase-ssl-web-site.md) pro vlastní doménu aplikace nebo [Vytvořte vazby na certifikát třetí strany](app-service-web-tutorial-custom-ssl.md).
    - **Spřažení ARR**: V nasazení s více instancemi zajistěte, aby byl klient směrován do stejné instance po dobu životnosti relace. Tuto možnost můžete nastavit na **vypnuto** u bezstavových aplikací.
- **Ladění**: Povolte vzdálené ladění pro aplikace [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)nebo [Node. js](containers/configure-language-nodejs.md#debug-remotely) . Tato možnost se automaticky vypne po 48 hodinách.
- **Příchozí klientské certifikáty**: vyžadují klientské certifikáty při [vzájemném ověřování](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurovat výchozí dokumenty

Toto nastavení platí jenom pro aplikace pro Windows.

V [Azure Portal]přejděte na stránku správy aplikace. V nabídce vlevo aplikace klikněte na**výchozí dokumenty** **Konfigurace** > .

![Obecná nastavení](./media/configure-common/open-documents.png)

Výchozí dokument je webová stránka, která se zobrazuje na kořenové adrese URL webu. Použije se první vyhovující soubor v seznamu. Chcete-li přidat nový výchozí dokument, klikněte na tlačítko **Nový dokument**. Nezapomeňte kliknout na **Uložit**.

Pokud aplikace používá moduly, které směrují na základě adresy URL namísto obsluhy statického obsahu, nemusíte používat výchozí dokumenty.

## <a name="configure-path-mappings"></a>Konfigurace mapování cest

V [Azure Portal]přejděte na stránku správy aplikace. V levé nabídce aplikace klikněte na**mapování cest** **Konfigurace** > .

![Obecná nastavení](./media/configure-common/open-path.png)

Na stránce **mapování cest** se zobrazují různé věci podle typu operačního systému.

### <a name="windows-apps-uncontainerized"></a>Aplikace pro Windows (nekontejnerované)

Pro aplikace pro Windows můžete přizpůsobit mapování obslužných rutin služby IIS a virtuální aplikace a adresáře.

Mapování obslužných rutin umožňují přidat vlastní skriptové procesory, které budou zpracovávat požadavky na konkrétní přípony souborů. Chcete-li přidat vlastní obslužnou rutinu, klikněte na tlačítko **Nová obslužná rutina**. Nastavte obslužnou rutinu následujícím způsobem:

- **Přípona**. Přípona souboru, kterou chcete zpracovat, například  *\*. php* nebo *obslužných rutin. fcgi*.
- **Procesor skriptů**. Absolutní cesta k procesoru skriptu. Požadavky na soubory, které odpovídají příponám souborů, zpracovává procesor skriptu. Použijte cestu `D:\home\site\wwwroot` pro odkaz na kořenový adresář vaší aplikace.
- **Argumenty**. Volitelné argumenty příkazového řádku pro procesor skriptu.

Každá aplikace má výchozí kořenovou cestu (`/`) namapovanou `D:\home\site\wwwroot`na, kde je váš kód nasazený ve výchozím nastavení. Pokud je kořenový adresář aplikace v jiné složce nebo pokud vaše úložiště obsahuje více než jednu aplikaci, můžete zde upravit nebo přidat virtuální aplikace a adresáře. Klikněte na **Nová virtuální aplikace nebo adresář**.

Chcete-li konfigurovat virtuální aplikace a adresáře, zadejte každý virtuální adresář a jeho odpovídající fyzickou cestu vzhledem k kořenovému`D:\home`adresáři webu (). Volitelně můžete zaškrtnout políčko **aplikace** a označit tak virtuální adresář jako aplikaci.

### <a name="containerized-apps"></a>Kontejnerové aplikace

Můžete [Přidat vlastní úložiště pro svou kontejnerovou aplikaci](containers/how-to-serve-content-from-azure-storage.md). Kontejnerové aplikace zahrnují všechny aplikace pro Linux a také vlastní kontejnery pro Windows a Linux běžící na App Service. Klikněte na **nový Azure Storage připojit** a nakonfigurujte vlastní úložiště následujícím způsobem:

- **Název**: Zobrazované jméno
- **Možnosti konfigurace**: **Basic** nebo **Advanced**.
- **Účty úložiště**: Účet úložiště s kontejnerem, který chcete.
- **Typ úložiště**: **Azure Blob** nebo **soubory Azure**.
  > [!NOTE]
  > Aplikace služby Windows Container podporují pouze soubory Azure.
- **Kontejner úložiště**: Pro základní konfiguraci se jedná o kontejner, který chcete.
- **Název sdílené složky**: Pro pokročilou konfiguraci se jedná o název sdílené složky.
- **Přístupový klíč**: Pro pokročilou konfiguraci se jedná o přístupový klíč.
- **Cesta pro připojení**: Absolutní cesta ve vašem kontejneru pro připojení vlastního úložiště

Další informace najdete v tématu [obsluha obsahu z Azure Storage v App Service v systému Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurovat nastavení zásobníku jazyka

Pro aplikace pro Linux se podívejte na:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurace vlastních kontejnerů

Další informace najdete v tématu [Konfigurace vlastního kontejneru Linux pro Azure App Service](containers/configure-custom-container.md) .

## <a name="next-steps"></a>Další kroky

- [Konfigurace vlastní domény ve službě Azure App Service]
- [Nastavení přípravných prostředí ve službě Azure App Service]
- [Povolení HTTPS pro aplikaci v Azure App Service]
- [Povolit diagnostické protokoly](troubleshoot-diagnostic-logs.md)
- [Horizontální navýšení kapacity aplikace v Azure App Service]
- [Základy monitorování v Azure App Service]
- [Změna nastavení souboru applicationHost. config pomocí souboru applicationHost. xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET signál]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurace vlastní domény ve službě Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Nastavení přípravných prostředí ve službě Azure App Service]: ./deploy-staging-slots.md
[Povolení HTTPS pro aplikaci v Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Základy monitorování v Azure App Service]: ./web-sites-monitor.md
[Režim kanálu]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Horizontální navýšení kapacity aplikace v Azure App Service]: ./manage-scale-up.md
