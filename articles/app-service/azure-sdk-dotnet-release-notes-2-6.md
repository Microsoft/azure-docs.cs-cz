---
title: Zpráva k vydání verze Azure SDK pro .NET 2.6
description: Zpráva k vydání verze Azure SDK pro .NET 2.6
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 040eea1efd3abdbbfa65c24b2950ebece9d4acbc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258387"
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Zpráva k vydání verze Azure SDK pro .NET 2.6
Tento dokument obsahuje poznámky k verzi pro Windows Azure SDK for .NET 2.6 vydání. 

Azure SDK 2.6 vám umožní vytvářet cloudové aplikace služby (PaaS) cílí na .NET 4.5.2 nebo rozhraní .NET 4.6, za předpokladu, že je ručně nainstalovat cílového rozhraní .NET Framework v roli cloudové služby. Zobrazit [instalace rozhraní .NET v roli cloudové služby](https://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Aktualizace pro Service Bus
* Event Hubs: 
  
  * Teď umožňuje řízení přístupu na cílové při odesílání událostí odhalení dalších vydavatele koncový bod pro službu Event Hubs.
  * Zvýšení stability a zlepšování přidána do funkce Event Hubs.
  * Přidání podpory protokolu Amqp přes WebSocket pro zasílání zpráv a Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Nástroje HDInsight pro aktualizace sady Visual Studio
* **Vylepšení IntelliSense**: návrh vzdálených metadat
  
    Nástroje HDInsight pro Visual Studio teď podporuje načítání vzdálených metadat při úpravách skriptu Hive. Například můžete zadat **vybrat * z** a zobrazí se všechny názvy tabulek. Také názvy sloupců se zobrazí po zadání tabulku.
* **Podpora emulátoru HDInsight**
  
    Připojování k emulátoru HDInsight teď podporují nástroje HDInsight pro Visual Studio, tak může vyvíjet skriptů Hive místně bez vnášení žádné poplatky, potom spusťte tyto skripty na clusterech služby HDInsight. 
  
    Další informace najdete v [této příručky](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **Nástroje HDInsight pro Visual Studio podporují pro obecný clustery Hadoop** (Preview)
  
    HDInsight Tools for Visual Studio teď podporují obecný clusterů Hadoop, abyste mohli používat HDInsight Tools for Visual Studio provádět následující akce:
  
  * připojení k vašemu clusteru 
  * napsat dotaz Hive s rozšířenou podporu technologie IntelliSense a automatické dokončování 
  * Zobrazte všechny úlohy ve vašem clusteru pomocí intuitivního uživatelského rozhraní. 
    
    Další informace najdete v [této příručky](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Aktualizace mezipaměti in-Role
* **Mezipaměť in-Role** byl aktualizován na použití **sadu SDK služby Microsoft Azure Storage** verze 4.3. Až doteď **mezipaměť In-Role** používal Azure Storage SDK verze 1.7.
  
    Zákazníci pomocí Azure SDK 2.5 nebo níže by měla aktualizovat na Azure SDK 2.6 a přesunout na novou verzi sady SDK služby Azure Storage. 
  
    V tuto chvíli je Azure Storage verze 2011-08-18 naplánovat odebrání 1. srpna 2016. Žádné migrace v instanci Role z Azure SDK 2.5 nebo verzi uvedenou níže na 2.6 musí být úplné té doby. Další informace o vyřazení služby Azure Storage verze 2011-08-18 najdete v tématu [odebrání aktualizace služby verzi Microsoft Azure Storage: rozšíření na 2016](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Oznamujeme vyřazení z provozu 30. listopadu 2016, pro službu Azure Managed Cache Service a mezipaměť In-Role Azure. Doporučujeme migrovat na Azure Redis Cache během přípravy na toto vyřazení z provozu. Další informace o data a pokyny k migraci, najdete v části [z variant mezipaměti Azure který je pro mě nejlepší?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Nástroje služby Azure App Service
Následující položky byly aktualizovány ve vydané verzi Azure SDK 2.6.

* Publikování Azure rozšířeného zahrnout aplikace API Azure jako cíle nasazení.
* API Apps zřizování funkce umožňuje uživatelům s funkcemi vytváření a zřizování aplikace API.
* Průzkumník serveru změnit tak, aby odrážely nový uzel App Service, se webové, mobilní a API apps seskupené podle skupiny prostředků.
* Přidejte gesto klienta aplikace Azure API přidat k většině C# projekty, které vám umožní automatické generování podporou Swaggeru API Apps spuštěné v Azure předplatné uživatele.
* Uzly služby App Service v Průzkumníku serveru a nástroje pro API Apps jsou k dispozici v sadě Visual Studio 2013 pouze. 

## <a name="azure-resource-manager-tools-updates"></a>Aktualizace nástroje Azure Resource Manageru
Nástroje Azure resource Manageru se aktualizovaly zahrnout šablony pro virtuální počítače, sítě a úložiště. Aktualizovali jsme prostředí pro úpravy kódu JSON zahrnout nové zobrazení osnovy šablony a možnostem úprav šablony pomocí fragmentů kódu JSON. Šablony nasazení ze sady Visual Studio pomocí skriptu prostředí PowerShell, který je součástí projektu, takže všechny změny provedené skriptu se použije sada Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Vylepšení diagnostiky pro cloudové služby
Azure SDK 2.6 zpět přináší podporu pro diagnostické protokoly v emulátoru Azure compute shromažďování a přenášení k vývojovým úložištěm. Žádné diagnostické protokoly (včetně protokolů, událostí trasování pro Windows (ETW) protokoly, čítače výkonu, infrastruktury protokoly událostí systému windows a protokolování trasování aplikace) vygenerované když aplikace běží v emulátoru lze přenést do vývojovým úložištěm Chcete-li ověřit, že diagnostického protokolování pracuje na svém místním počítači. 

Účet úložiště diagnostiky teď můžou být specifikované v souboru (.csdef) služby, snadnější používání účtů úložiště diagnostiky různých pro různá prostředí. Existují některé důležité rozdíly mezi jak v Azure SDK 2.4 a Azure SDK 2.6 fungoval připojovací řetězec. Další informace o tom, jak použít připojovací řetězec úložiště diagnostiky a jak to ovlivní vaše projekty naleznete v tématu [konfiguraci diagnostiky pro Azure Cloud Services](https://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Změny způsobující chyby
### <a name="azure-resource-manager-tools"></a>Nástroje Azure Resource Manageru
* **Projektech s Cloudovým nasazením** typ projektu, které jsou k dispozici v Azure SDK 2.5 byl přejmenován na **skupiny prostředků Azure**.
* **Projekty nasazení cloudu** typu projekty vytvořené v Azure SDK 2.5 je možné použít ve verzi 2.6 ale nasazení šablony ze sady Visual Studio se nezdaří. Nicméně nasazení pomocí skriptu prostředí PowerShell budou i nadále fungovat stejně jako dříve.  Informace o tom, jak používat **projektech s Cloudovým nasazením** ve verzi 2.6 najdete v tomto [příspěvku](https://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Známé problémy
* Shromažďují se protokoly diagnostiky se spustila v emulátoru vyžaduje 64bitový operační systém. Při spuštění v 32bitové verzi operačního systému nebudou shromažďovat diagnostické protokoly. Další funkce emulátor to nemá vliv. 
* 4/29/2015 vydala sadu Azure SDK 2.6 obsahovala dva problémy: 
  
  * Univerzální aplikace pro nebylo možné načíst v sadě Visual Studio 2015, když Azure SDK 2.6 byl nainstalován na počítači.
  * Ladění projektu cloudové služby selže ve Visual Studio 2013 a Visual Studio 2015, Visual Studio přestane reagovat kde dojde k chybě při zobrazení dialogového okna se zprávou "Konfigurace Diagnostika pro emulátor".
    
    18. 5/2015 byla vydána aktualizace Azure SDK 2.6. 2.6.30508.1601; je aktualizovaná verze obsahuje opravy pro dva problémy popsané výše. Můžete určit sestavení sady SDK z ovládacích panelů -> programy a funkce -> nástroje Microsoft Azure pro Microsoft Visual Studio 2013 – v 2.6. Číslo sestavení se zobrazí sloupec verze.
    
    Pokud stále máte výše uvedené problémy, nainstalujte nejnovější verzi sady Azure SDK 2.6 pro [VS 2012](https://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](https://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) nebo [VS 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Viz také
[Podpora a informace o vyřazení sady Azure SDK pro .NET a rozhraní API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

