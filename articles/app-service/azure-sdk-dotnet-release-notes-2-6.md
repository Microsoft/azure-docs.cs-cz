---
title: Poznámky k verzi sady Azure SDK pro .NET 2.6
description: Poznámky k verzi sady Azure SDK pro .NET 2.6
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
ms.openlocfilehash: 21817b09440fc98a54dc45c9129d104b01fa387d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836459"
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Poznámky k verzi sady Azure SDK pro .NET 2.6
Tento dokument obsahuje poznámky k verzi sady Azure SDK pro .NET 2.6 verze. 

S Azure SDK 2.6 můžete vyvíjet cílení na rozhraní .NET 4.5.2 nebo .NET 4.6 za předpokladu, že je ručně nainstalovat cílové rozhraní .NET Framework pro Cloudovou roli služby aplikace cloudové služby (PaaS). V tématu [instalace rozhraní .NET v roli služby Cloud](http://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Aktualizace služby Service Bus
* Centra událostí: 
  
  * Teď umožňuje řídit cílové přístup při odesílání událostí podle vystavení další vydavatele koncový bod pro službu Event Hubs.
  * Zvýšení stability a zlepšování přidat do centra událostí funkce.
  * Přidání podpory protokolu Amqp přes protokol WebSocket pro zasílání zpráv a Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Aktualizace nástroje HDInsight pro Visual Studio
* **Vylepšení IntelliSense**: návrh vzdálených metadat
  
    Nástroje HDInsight pro Visual Studio teď podporuje získávání vzdálených metadat při úpravách skriptu Hive. Například můžete zadat **vyberte * FROM** a zobrazí se všechny názvy tabulek. Taky názvy sloupců se zobrazí po zadání tabulku.
* **Podporu emulátoru HDInsight**
  
    Nyní nástroje HDInsight pro Visual Studio podporují připojení k emulátoru HDInsight, takže skripty Hive může vytvořit místně bez zavedení žádné náklady, potom spusťte tyto skripty proti clusterům HDInsight. 
  
    Další informace najdete v části [této příručky](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **Nástroje HDInsight pro Visual Studio podporují pro obecné clusterů systému Hadoop** (Preview)
  
    Nástroje HDInsight pro Visual Studio teď podporují obecné clusterů systému Hadoop, abyste mohli používat nástroje HDInsight pro Visual Studio k následujícím:
  
  * připojit ke clusteru, 
  * Zápis dotazů Hive s rozšířenou podporu technologie IntelliSense nebo automatické dokončování 
  * Zobrazte všechny úlohy v clusteru s intuitivní uživatelské rozhraní. 
    
    Další informace najdete v části [této příručky](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Aktualizace mezipaměti v roli
* **Mezipaměť hostovaná v instanci Role** byl upraven k použití **Microsoft Azure Storage SDK** verze 4.3. Až doteď **mezipaměť hostovaná v instanci Role** používal úložiště Azure SDK 1.7 verze.
  
    Zákazníci pomocí Azure SDK 2.5 nebo níže by měl aktualizovat Azure SDK 2.6 a přesunout na novou verzi sady Azure SDK úložiště. 
  
    V tuto chvíli je naplánováno Azure Storage verze 2011-08-18 odeberou 1 srpna 2016. Všechny migrace mezipaměť hostovaná v instanci Role z Azure SDK 2.5 nebo verzi uvedenou níže na 2.6 musí být úplný té doby. Další informace o vyřazení verze 2011-08-18 Azure Storage najdete v tématu [aktualizace verze odebrání úložiště služby Microsoft Azure: rozšíření pro 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Nemůžeme se uvedení 30. listopadu 2016, vyřazení služby mezipaměti spravované Azure a Azure v roli Cache. Doporučujeme, abyste přenést do Azure Redis Cache během přípravy na tento vyřazení. Další informace o kalendářních dat a migrace pokyny najdete v tématu [Azure Cache které nabídky je pro mě nejlepší?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Nástroje služby Azure App Service
Následující položky byly aktualizovány ve verzi Azure SDK 2.6.

* Vylepšené a zahrnují aplikace API Azure jako cíl nasazení Azure publikování.
* Aplikace API zřizování funkce umožníte uživatelům s funkcemi vytváření a zřizování aplikace API.
* V Průzkumníku serveru změnit tak, aby odrážela nový uzel App Service, se webové, mobilní a rozhraní API aplikace, které jsou seskupené podle skupiny prostředků.
* Přidáte klienta aplikace Azure API gesto přidány do většina C# projekty, které vám umožní automatické generování povoleno Swagger rozhraní API aplikace běžící v rámci předplatného Azure uživatele.
* Pouze jsou k dispozici v sadě Visual Studio 2013 nástrojů aplikace API a uzly služby App Service v Průzkumníku serveru. 

## <a name="azure-resource-manager-tools-updates"></a>Aktualizace nástroje Azure Resource Manager
Nástroje Správce prostředků Azure se aktualizovaly v rámci zahrnují šablony pro virtuální počítače, sítě a úložiště. Zahrnout nové zobrazení osnovy šablon a možnost upravovat šablony použití fragmentů kódu JSON se aktualizovalo JSON prostředí pro úpravy. Šablony nasazení ze sady Visual Studio pomocí skriptu prostředí PowerShell, který je součástí projektu, takže všechny změny do skriptu se použije v sadě Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Vylepšení diagnostiky pro cloudové služby
Azure SDK 2.6 zobrazí zpět podporu pro shromažďování protokolů diagnostiky v emulátor výpočtů v Azure a jejich přenos do úložiště vývoj. Všechny diagnostické protokoly (včetně protokolů událostí trasování pro protokoly systému Windows (ETW), čítače výkonu, infrastruktury protokoly událostí systému windows a protokolování trasování aplikace) generované když aplikace běží v emulátoru dá přenést na vývoj úložiště k ověření, že vaše protokolování diagnostiky funguje na místním počítači. 

Účet úložiště diagnostiky lze nyní v souboru konfigurace (.cscfg) služby snadnější používání různých diagnostiky účty úložiště v různých prostředích. Existují některé významné rozdíly mezi jak připojovací řetězec fungovala Azure SDK 2.4 a Azure SDK 2.6. Další informace o použití připojovacího řetězce úložiště diagnostiky a jak se má dopad na vašich projektů najdete v části [konfigurace diagnostiky pro Azure Cloud Services](http://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Nejnovější změny
### <a name="azure-resource-manager-tools"></a>Nástroje Azure Resource Manager
* **Projekty nasazení cloudu** typu projektu, které jsou k dispozici v Azure SDK 2.5 byl přejmenován na **skupiny prostředků Azure**.
* **Projekty nasazení cloudu** typ projektů vytvořených v Azure SDK 2.5 může být použit ve verzi 2.6 ale nasazení šablony ze sady Visual Studio se nezdaří. Ale nasazení pomocí skriptu prostředí PowerShell budou i nadále fungovat stejně jako dříve.  Informace o tom, jak používat **projekty nasazení cloudu** ve verzi 2.6 načíst tato [post](http://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Známé problémy
* Shromažďování protokolů diagnostiky v emulátoru vyžaduje 64bitový operační systém. Diagnostické protokoly nebudou shromažďovány, pokud se používá na 32bitové verzi operačního systému. Toto nastavení neovlivní žádné další funkce emulátor. 
* Azure SDK 2.6 vydané 4/29/2015 měl dva problémy: 
  
  * Univerzální aplikace nelze načíst v sadě Visual Studio 2015, pokud Azure SDK 2.6 byl nainstalován na počítači.
  * Ladění projektu cloudové služby skončí s chybou v sadě Visual Studio 2013 a Visual Studio 2015, kde Visual Studio přestane reagovat a dojde k chybě při zobrazování dialogové okno se zprávou "Konfigurace diagnostiky pro emulátor".
    
    Aktualizace Azure SDK 2.6 byla vydána 18 5. 2015. Aktualizovaná verze je 2.6.30508.1601; obsahuje opravy pro dva problémy popsané výše. Můžete identifikovat sestavení sady SDK z ovládací panely -> programy a funkce -> nástroje Microsoft Azure pro Microsoft Visual Studio 2013 – v 2.6. Sloupec verze se zobrazí číslo sestavení.
    
    Pokud se stále setkávají uvedené problémy, nainstalujte nejnovější verzi sady Azure SDK 2.6 pro [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) nebo [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Viz také
[Podporu a informace o vyřazení pro sadu Azure SDK pro .NET a rozhraní API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

