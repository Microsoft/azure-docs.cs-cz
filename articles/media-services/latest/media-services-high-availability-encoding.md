---
title: Vysoká dostupnost s Media Services videem na vyžádání
description: Tento článek představuje přehled služeb Azure, které můžete použít k usnadnění vysoké dostupnosti aplikace VOD.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2b0158c3b1bbee37fdb10c8fc0131be580ad6fc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562609"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Vysoká dostupnost pomocí Media Services a videa na vyžádání (VOD)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>Vysoká dostupnost pro VOD

V dokumentaci k architektuře Azure existuje vzor návrhu vysoké dostupnosti s názvem [Geodes](/azure/architecture/patterns/geodes) . Popisuje způsob nasazení duplicitních prostředků do různých geografických oblastí za účelem zajištění škálovatelnosti a odolnosti.  Pomocí služeb Azure můžete vytvořit takovou architekturu, která bude pokrývat různé faktory pro návrh vysoké dostupnosti, jako je redundance, monitorování stavu, Vyrovnávání zatížení a zálohování a obnovení dat.  Jedna taková architektura je popsaná níže s podrobnostmi o každé službě použité v řešení a také o tom, jak se jednotlivé služby dají použít k vytvoření architektury vysoké dostupnosti pro vaši aplikaci VOD.

### <a name="sample"></a>Ukázka

K dispozici je ukázka, kterou můžete použít k seznámení s vysokou dostupností pomocí Media Services a videa na vyžádání (VOD). Také se seznámíte s tím, jak se služby používají pro VOD scénář.  Vzorek není určen pro použití v produkčním prostředí v jeho současné podobě.  Pečlivě si přečtěte ukázkový kód a soubor Readme, zejména část o [režimech selhání](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) před integrací do produkční aplikace.  Provozní implementace vysoké dostupnosti pro video na vyžádání (VOD) by měla pečlivě zkontrolovat jejich strategii Content Delivery Network (CDN).  Podívejte se na [kód na GitHubu](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## <a name="overview-of-services"></a>Přehled služeb

Mezi služby použité v tomto příkladu architektury patří:

| Ikona | Název | Description |
| :--: | ---- | ----------- |
|![Toto je ikona účtu Media Services.](media/media-services-high-availability-encoding/azure-media-services.svg)| Účet Media Services | **Popis:**<br>Media Services účet je výchozím bodem pro správu, šifrování, kódování, analýzu a streamování mediálního obsahu v Azure. Je spojen s prostředkem účtu Azure Storage. Účet a všechny přidružené úložiště musí být ve stejném předplatném Azure.<br><br>**VOD použít:**<br>Jedná se o služby, které používáte ke kódování a doručování videí a zvukových prostředků.  Pro zajištění vysoké dostupnosti byste nastavili aspoň dva Media Services účty, z nichž každá je v jiné oblasti. [Přečtěte si další informace o Azure Media Services](media-services-overview.md). |
|![Toto je ikona účtu úložiště.](media/media-services-high-availability-encoding/storage-account.svg)| Účet úložiště | **Popis:**<br>Účet úložiště Azure obsahuje všechny datové objekty Azure Storage: objekty blob, soubory, fronty, tabulky a disky. Data jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS.<br><br>Každý Media Services účet by měl v každé oblasti účet úložiště ve stejné oblasti.<br><br>**VOD použít:**<br>Vstupní a výstupní data můžete ukládat pro zpracování VOD a streamování. [Přečtěte si další informace o Azure Storage](../../storage/common/storage-introduction.md). |
|![Toto je ikona fronty Azure Storage.](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure Queue Storage | **Popis:**<br>Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS.<br><br>**VOD použít:**<br>Fronty lze použít k posílání a přijímání zpráv pro koordinaci aktivit mezi různými moduly. Ukázka používá Azure Storage frontu, ale Azure poskytuje další typy front, například Service Bus a Service Fabric spolehlivé fronty, které mohou lépe vyhovovat vašim potřebám. [Přečtěte si další informace o frontě Azure](../../storage/queues/storage-queues-introduction.md). |
|![Toto je ikona Azure Cosmos DB.](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **Popis:**<br>Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů, která nezávisle škáluje propustnost a úložiště napříč libovolným počtem oblastí Azure po celém světě.<br><br>**VOD použít:**<br>Tabulky lze použít k ukládání záznamů stavu výstupu úlohy a ke sledování stavu každé instance Media Services. Můžete také sledovat nebo zaznamenávat stav každého volání rozhraní Media Services API. [Přečtěte si další informace o Azure Cosmos DB](../../cosmos-db/introduction.md).  |
|![Toto je ikona spravované identity.](media/media-services-high-availability-encoding/managed-identity.svg)| Spravovaná identita | **Popis:**<br>Spravovaná identita je funkce služby Azure AD, která poskytuje automaticky spravovanou identitu ve službě Azure AD. Dá se použít k ověření jakékoli služby, která podporuje ověřování Azure AD, včetně Key Vault, bez uložení přihlašovacích údajů do kódu.<br><br>**VOD použít:**<br>Azure Functions můžou použít spravovanou identitu k ověření, aby se Media Services instance připojovaly k Key Vault. [Přečtěte si další informace o spravované identitě](../../active-directory/managed-identities-azure-resources/overview.md). |
|![Toto je ikona Key Vault.](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **Popis:**<br>Azure Key Vault lze použít k bezpečnému ukládání a pečlivému řízení přístupu k tokenům, heslům, certifikátům, klíčům rozhraní API a dalším tajným klíčům. Dá se použít taky jako řešení správy klíčů. Azure Key Vault usnadňuje vytváření a správu šifrovacích klíčů sloužících k šifrování dat. Může snadno zřídit, spravovat a nasazovat veřejné a soukromé certifikáty TLS/SSL (Transport Layer Security/SSL (Secure Sockets Layer)) pro použití s Azure a interními připojenými prostředky. Tajné kódy a klíče lze chránit buď pomocí softwaru, nebo ověřeného HSM úrovně Standard FIPS 140-2 úrovně 2.<br><br>**VOD použít:**<br>Key Vault můžete použít k nastavení zásad přístupu pro instanční objekt pro vaši aplikaci.  Dá se použít k uložení připojovacího řetězce do účtů úložiště. K ukládání připojovacích řetězců do účtů úložiště a Cosmos DB používáme Key Vault. K uložení celkové konfigurace clusteru můžete použít taky Key Vault. Pro každou instanci služby Media Service můžete uložit ID předplatného, název skupiny prostředků a název účtu. Další podrobnosti najdete v tématu Jak se používá v ukázce. [Přečtěte si další informace o Key Vault](../../key-vault/general/overview.md). |
|![Toto je ikona Azure Functions.](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **Popis:**<br>Spouštějte malé části kódu (označované jako "funkce"), aniž byste se museli starat o infrastrukturu aplikace pomocí Azure Functions. [Přečtěte si další informace o Azure Functions](../../azure-functions/functions-overview.md).<br><br>**VOD použít:**<br>Azure Functions lze použít k uložení hostitelských modulů aplikace VOD.  Moduly pro aplikaci VOD mohou zahrnovat:<br><br>**Modul plánování úloh**<br>Modul plánování úloh by byl pro odeslání nových úloh do clusteru Media Services (nejméně dvě instance v různých oblastech). Mohl by sledovat stav každé instance Media Services a měl by Odeslat novou úlohu do další dobré instance.<br><br>**Modul stavu úlohy**<br>Modul stavu úlohy bude naslouchat událostem stavu výstupu úlohy přicházejících z Azure Event Grid služby. Uloží události do úložiště událostí, aby se minimalizoval počet volání rozhraní Media Services API po zbytek modulů.<br><br>**Modul stavu instance**<br>Tento modul bude sledovat odeslané úlohy a zjišťovat stav jednotlivých instancí Media Services. Sleduje dokončené úlohy, neúspěšné úlohy a úlohy, které se nikdy nedokončily.<br><br>**Modul zřizování**<br>Tento modul zřídí zpracované prostředky. Může zkopírovat data prostředků do všech instancí Media Services a nastavit službu Azure front-dveří, aby se zajistilo streamování prostředků i v případě, že nebyly k dispozici některé Media Services instance. Nastavily se také Lokátory streamování.<br><br>**Modul ověření úlohy**<br>Tento modul bude sledovat každou odeslanou úlohu, znovu odeslat neúspěšné úlohy a po úspěšném dokončení úlohy provést vyčištění dat úlohy.  |
|![Toto je ikona App Service.](media/media-services-high-availability-encoding/application-service.svg)| App Service (a plán)  | **Popis:**<br>Azure App Service je služba založená na protokolu HTTP pro hostování webových aplikací, rozhraní REST API a mobilní back-endy. Podporuje .NET, .NET Core, Java, Ruby, Node.js, PHP nebo Python. Aplikace běží a škálují v prostředích založených na systému Windows i Linux.<br><br>**VOD použít:**<br>Každý modul by byl hostovaný App Service. [Přečtěte si další informace o App Service](../../app-service/overview.md). |
|![Toto je ikona přední dveře Azure.](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **Popis:**<br>Přední dvířka Azure slouží k definování, správě a monitorování globálního směrování webového provozu optimalizací pro nejlepší výkon a rychlé globální převzetí služeb při selhání pro zajištění vysoké dostupnosti.<br><br>**VOD použít:**<br>K směrování provozu do koncových bodů streamování se dají použít přední dvířka Azure. [Přečtěte si další informace o službě Azure front-dveří](../../frontdoor/front-door-overview.md).  |
|![Toto je ikona Azure Event Grid.](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure Event Grid | **Popis:**<br>Služba Event Grid pro architektury založené na událostech vytvořila integrovanou podporu pro události přicházející ze služeb Azure, jako jsou objekty blob úložiště a skupiny prostředků. Má také podporu pro vlastní události tématu. Filtry lze použít ke směrování konkrétních událostí do různých koncových bodů, vícesměrového vysílání do více koncových bodů a k zajištění spolehlivého doručování událostí. Maximalizuje dostupnost tím, že nativně rozprostře mezi několik domén selhání v každé oblasti a v různých zónách dostupnosti.<br><br>**VOD použít:**<br>Event Grid lze použít ke sledování všech událostí aplikace a jejich uložení do stavu úlohy. [Přečtěte si další informace o Azure Event Grid](../../event-grid/overview.md). |
|![Toto je ikona Application Insights.](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **Popis:** <br>Application Insights je funkcí Azure Monitoru. Je to rozšiřitelná služba pro správu výkonu aplikací (APM) pro vývojáře a odborníky na DevOps. Slouží k monitorování živých aplikací. Detekuje anomálie výkonu a obsahuje analytické nástroje pro diagnostiku problémů a pochopení toho, co uživatelé s aplikací dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.<br><br>**VOD použít:**<br>Všechny protokoly je možné odeslat do Application Insights. Vyhledáním úspěšně vytvořených zpráv úlohy by bylo možné zjistit, která instance zpracovala jednotlivé úlohy. Může obsahovat všechna odeslaná metadata úlohy včetně jedinečného identifikátoru a informací o názvu instance. [Přečtěte si další informace o Application Insights](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Architektura

Tento diagram vysoké úrovně ukazuje architekturu ukázky, která vám umožní začít s vysokou dostupností a Media Services.

[![Diagram ](media/media-services-high-availability-encoding/high-availability-architecture.svg) architektury vysoké úrovně videa na vyžádání (vod)](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>Osvědčené postupy

### <a name="regions"></a>Oblasti

* [Vytvořte](./create-account-howto.md) dva (nebo více) Azure Media Services účty. Tyto dva účty se musí nacházet v různých oblastech. Další informace najdete v tématu [oblasti, ve kterých je nainstalovaná služba Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Nahrajte médium do stejné oblasti, ze které plánujete úlohu odeslat. Další informace o tom, jak spustit kódování, najdete v tématu [Vytvoření vstupu úlohy z adresy URL https](./job-input-from-http-how-to.md) nebo [Vytvoření vstupu úlohy z místního souboru](./job-input-from-local-file-how-to.md).
* Pokud budete později potřebovat [úlohu](./transforms-jobs-concept.md) znovu odeslat do jiné oblasti, můžete ji použít `JobInputHttp` nebo použít `Copy-Blob` ke kopírování dat z kontejneru zdrojových prostředků do kontejneru assetů v alternativní oblasti.

### <a name="monitoring"></a>Monitorování

* Přihlášení k odběru `JobStateChange` zpráv v každém účtu prostřednictvím Azure Event Grid.
    * [Zaregistrujte se na události](./reacting-to-media-services-events.md) prostřednictvím Azure Portal nebo rozhraní příkazového řádku (můžete to provést také pomocí sady Event Grid Management SDK).
    * Použijte [sadu Microsoft. Azure. EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (která v nativním režimu podporuje události Media Services).
    * Můžete také spotřebovávat Event Grid události prostřednictvím Azure Functions.

    Další informace najdete tady:

    * Podívejte se na [ukázku audio Analytics](./transforms-jobs-concept.md) , kde se dozvíte, jak monitorovat úlohu pomocí Azure Event Grid, včetně přidání záložního pro případ, že Azure Event Grid zprávy z nějakého důvodu jsou zpožděny.
    * Podívejte se na [Azure Event Grid schémat pro Media Services události](./media-services-event-schemas.md).

* Při vytváření [úlohy](./transforms-jobs-concept.md):
    * Náhodně vyberte účet ze seznamu aktuálně používaných účtů (Tento seznam bude normálně obsahovat oba účty, ale pokud se zjistí problémy, může obsahovat jenom jeden účet). Pokud je seznam prázdný, vyvolejte výstrahu, aby mohl operátor prozkoumat.
    * Vytvořte záznam, který bude sledovat každou úlohu inletu a použitou oblast nebo účet.
* Když `JobStateChange` obslužná rutina získá oznámení, že úloha dosáhla naplánovaného stavu, zaznamenejte čas, který vstoupí do plánovaného stavu, a na oblast nebo účet, který jste použili.
* Když `JobStateChange` obslužná rutina získá oznámení, že úloha dosáhla stavu zpracování, označte záznam pro úlohu jako zpracování a zaznamenejte čas, který vstoupí do stavu zpracování.
* Když `JobStateChange` obslužná rutina získá oznámení, že úloha dosáhla konečného stavu (dokončeno/chyba/zrušeno/zrušeno), označte záznam pro úlohu odpovídajícím způsobem.
* Mít samostatný proces, který pravidelně prohledává vaše záznamy o úlohách.
    * Pokud máte úlohy v plánovaném stavu, které nepokročily do stavu zpracování v přiměřené době pro danou oblast, odeberte tuto oblast ze seznamu aktuálně používaných účtů. V závislosti na vašich obchodních požadavcích se můžete rozhodnout tyto úlohy okamžitě zrušit a znovu je odeslat do jiné oblasti. Nebo můžete jiným uživatelům poskytnout další čas na přechod k dalšímu stavu.
    * Pokud byla oblast odebrána ze seznamu účtů, před přidáním zpět do seznamu ji monitorujte pro obnovení. Regionální stav se dá monitorovat prostřednictvím stávajících úloh v oblasti (Pokud se nezrušily a znovu neodeslaly), a to přidáním účtu zpátky do seznamu po určitou dobu a pomocí operátorů sledujících komunikaci Azure o výpadkech, které by mohly ovlivnit Azure Media Services.

## <a name="next-steps"></a>Další kroky

* Podívejte se na [ukázky kódu](/samples/browse/?products=azure-media-services)