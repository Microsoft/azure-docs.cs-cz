---
title: Zpráva k vydání verze Azure SDK pro .NET 2.7 a .NET 2.7.1
description: Zpráva k vydání verze Azure SDK pro .NET 2.7 a .NET 2.7.1
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 4c5a4dfcdde91d1bd0c2728ff9d071d4c2f73f0e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969766"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Zpráva k vydání verze Azure SDK pro .NET 2.7 a .NET 2.7.1
## <a name="overview"></a>Přehled
Tento dokument obsahuje poznámky k verzi Azure SDK pro .NET 2.7 vydání. 

Dokument také obsahovat zpráva k vydání verze pro sadu Azure SDK pro .NET 2.7.1 vydané verze.

Azure SDK 2.7 je podporována pouze v sadě Visual Studio 2015 a Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) není poslední sadu SDK pro Visual Studio 2012.

Podrobné informace o této verzi najdete v tématu [Azure SDK 2.7 oznámení příspěvku](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) a [sady Azure SDK 2.7.1 oznámení příspěvku](https://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK pro .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Přihlaste se vylepšení pro sadu Visual Studio 2015
Azure SDK 2.7 for Visual Studio 2015 podporuje nové funkce správy identit v sadě Visual Studio 2015.  To zahrnuje podporu pro účty přistupující k Azure prostřednictvím řízení přístupu na základě rolí, poskytovatele Cloud Solution Provider, DreamSpark a další typy účtů a předplatných.

Vylepšení přihlašování součástí Azure SDK 2.7 jsou dostupné jenom v sadě Visual Studio 2015. Podpora pro sadu Visual Studio 2013 je součástí sady Azure SDK 2.7.1.

### <a name="mobile-sdk"></a>Sada SDK pro mobilní
Aktualizovat **Mobile Apps** šablony tak, aby odrážely nejnovější [balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) a nastavení.

### <a name="service-bus"></a>Service Bus
Obecné opravy chyb a vylepšení. Podrobnosti o aktualizace a funkce, najdete v poznámkách k verzi z nejnovější [NuGet služby Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>Nástroje HDInsight
V této verzi byly provedeny následující aktualizace. Tyto aktualizace jsou ve verzi preview. Další informace najdete v tématu [tento blog](https://go.microsoft.com/fwlink/?LinkId=619108).

* Hive grafů pro Hive na úlohách Tez
* Plnou podporu Hive DML IntelliSense
* Podpora šablony pro pig
* Šablony Storm pro služby Azure

#### <a name="breaking-changes"></a>Změny způsobující chyby
* Staré **Storm** nutné upgradovat projekt, při použití této verze nástrojů. Další informace najdete v tématu [tento blog](https://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express se už nepodporuje. Další informace najdete v tématu [tento blog](https://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Nástroje služby Azure App Service
V této verzi byly provedeny následující aktualizace rozšíření nástroje pro Web. Další informace najdete v části [to](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogu. 

* Podpora pro DreamSpark účty přidané
* Provedené úplné změny do nástroje Azure pro podporu nové API Správce prostředků Azure
* Přidaná podpora pro Azure App Service [Průzkumník cloudu](#cloud_explorer)

#### <a name="known-issues"></a>Známé problémy
Uzly slot nasazení aplikace Web nejsou zobrazeny v uzlu sloty v Průzkumníku serveru a v části Průzkumník cloudu se nenačtou Web App deployment slot podřízené uzly. Tento problém byl vyřešen a připravené pro další verzi sady SDK. 

### <a name="cloud_explorer"></a>Cloud Explorer for Visual Studio 2015
Azure SDK 2.7 zahrnuje Cloud Explorer for Visual Studio 2015, která umožňuje zobrazit vaše prostředky Azure, zkoumání jejich vlastností a provádění klíčových vývojářských úkonů ze sady Visual Studio. 

Průzkumník cloudu podporuje následující funkce:

* Zobrazení skupiny prostředků a typ prostředku vašich prostředků Azure 
* Hledat prostředky podle názvu (k dispozici v zobrazení pro typ prostředku)
* Podpora pro předplatná a prostředky, které mají na základě řízení přístupu Role (RBAC) použít 
* Integrované panelu akcí, které zobrazuje akce zaměřené na vývojáře specifické pro vybrané prostředky. Příklad: připojit vzdálený ladicí program pro virtuálních počítačích vytvořených pomocí zásobníku správce prostředků zobrazit diagnostická data pro virtuální počítače atd.
* Integrované panel vlastnosti, které zobrazuje vlastnosti zaměřené na vývojáře běžně potřeby během vývoje a testování 
* Rychlé přepínání účet, který chcete použít při vytváření výčtu zdroje (použijte příkaz Nastavení na panelu nástrojů) 
* Filtrování předplatných pro použití při vytváření výčtu zdroje (použijte příkaz Nastavení na panelu nástrojů) 
* Přímé odkazy na webu Azure Portal ke správě prostředků a skupin prostředků 

### <a name="azure-resource-manager-tools"></a>Nástroje Azure Resource Manageru
Nástroje Azure Resource Manageru jsou aktualizované pro práci s na základě řízení přístupu Role (RBAC) a nové typy předplatného.  Součástí těchto změn je schopnost používat nové účty úložiště, kromě klasického úložiště k ukládání artefaktů během nasazení.  

Pokud používáte projekt skupiny prostředků Azure z předchozí verze sady SDK s SDK 2.7, nový skript nasazení je potřeba k nasazení pomocí nového účtu úložiště místo klasického úložiště.  Zobrazí se výzva, před provedením změn do projektu přidáte nový skript.  Původní skript bude přejmenován a budete muset ručně provádět žádné změny nový skript.

### <a name="storage-explorer-tools"></a>Storage Explorer nástroje
* Podpora pro zobrazení doplňovacích objektů BLOB. Další informace v [tento příspěvek na blogu](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Podpora pro zobrazení účty služby Premium Storage prostřednictvím Průzkumníka serveru. Průzkumník serveru budou zobrazovat jenom objekty BLOB stránky pro účty služby premium storage jsou jediným podporovaným typem pro účty služby premium storage.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Azure Data Factory Tools for Visual Studio
Představujeme **nástroje Azure Data Factory** pro sadu Visual Studio. Níže jsou povolené funkce. Zobrazit [tento blog](https://go.microsoft.com/fwlink/?LinkId=617530) Další informace.

* **Na základě šablony při vytváření**: výběr a použití na základě šablony, šablony pro přesun dat nebo zpracování dat šablony nasadit řešení integrace dat začátku do konce a začněte rychle s Data Factory. 
* **Integrace s Průzkumníkem řešení pro vytváření a nasazení entit služby Data Factory**: vytváření a nasazování kanálů a související entity jako projektů sady Visual Studio. 
* **Integrace pomocí zobrazení diagramu pro interakce vizuálů při vytváření**: vizuální vytváření kanálů a datových sad s podporou v zobrazení diagramu. 
* **Integrace s Průzkumníkem serveru pro procházení a interakce s entitami již nasazenou**: využít Průzkumníka serveru pro procházení, které jsou už nasazené datové továrny a odpovídajících entit. Importujte nasazené datové továrny nebo libovolnou entitu (kanál, propojené služby, datové sady) do projektu. 
* **Úpravy v editoru JSON pomocí schématu ověřování zpráv a formátovaných zpráv intellisense**: efektivní konfigurace a úpravy dokumentů JSON entit služby Data Factory s formátovaným technologie intellisense a schéma ověření 
* **Publikování několika prostředí**: publikování vytvořené kanály, abyste mohli vývojového, testovacího nebo produkčního prostředí tak, že vytvoříte samostatný konfigurační soubory pro každé prostředí.
* **Pig, Hive a .net založený na zpracování dat podporu**: podpora Pigu a Hivu skripty v projektu Data Factory. Podpora pro odkazování na C# projektu pro správu .net aktivity.

## <a name="azure-sdk-for-net-271"></a>Azure SDK pro .NET 2.7.1
Následující část obsahuje aktualizace, které byly představeny s nástrojem Azure SDK pro .NET 2.7.1 vydané verze.

### <a name="hdinsight-tools"></a>Nástroje HDInsight
Podrobnější vysvětlení o aktualizacích nástroje HDInsight, najdete v článku [tento blog](https://go.microsoft.com/fwlink/?LinkId=623831).

* Operátor zobrazení úloh Hive (nová funkce)
  
    Vám pomůžou pochopit dotaz Hive lépe, funkci zobrazení operátora Hive byla přidána. Pokud chcete zobrazit všechny operátory uvnitř vrcholu, dvakrát klikněte na vrcholy grafu úlohy. Chcete-li zobrazit podrobnosti o konkrétního operátora, podržte ukazatel myši nad tento operátor.
* Značky chyba Hive (nová funkce)
  
    Umožňuje zobrazit gramatické chyby okamžitě, Hive značky Chyba funkce byla přidána. Také, byly vylepšeny chybové zprávy a zobrazí se podrobné gramatické chyby okamžitě (až do této verzi musíte odeslat skript Hive do clusteru a počkejte chvíli před získávání podrobností o chybové zprávě).  
* Diagram topologie Storm (nová funkce)
  
    Vizualizace je velmi důležité, pokud chcete zobrazit, pokud vaše topologie funguje podle očekávání. V této verzi jsme přidali vizualizace pro Storm grafy. Můžete sledovat důležité metriky pro vaše topologie (například barva označuje počasí určité Bolt je "zaneprázdněný", nebo ne). Můžete také dvakrát kliknout Bolt/Spout zobrazíte další podrobnosti.
* Podpora pro clustery HDInsight, které byly vytvořeny na webu Azure Portal (opravy chyb)
  
    Visual Studio teď můžete k zobrazení a odesílání úloh ke clusterům HDInsight bez ohledu na to, kde byly vytvořeny clusteru.
* Další podpora IntelliSense a rychlejší Hive Metadata (zlepšení) načítání
  
    IntelliSense jsme vylepšili tak, že přidáte další návrhy uživatelsky přívětivější. Například aliasu tabulky můžete teď také navrhovat v technologii IntelliSense, můžete snadněji napsat dotaz. Navíc jsme vylepšili metadat Hive načítání tak bude trvat jenom několik sekund na seznamu všechny databáze, tabulky a sloupce vaše metastore Hive.

Podrobnější vysvětlení o aktualizacích nástroje HDInsight, najdete v článku [tento blog](https://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Vylepšení v sadě Visual Studio 2013
* Azure SDK 2.7.1 umožňuje sadě Visual Studio 2013 pro přístup k Azure účtů a předplatných prostřednictvím řízení přístupu na základě rolí, poskytovatele Cloud Solution Provider a Dreamspark.
* Sada Azure SDK 2.7.1 a nový panel nástrojů Průzkumník cloudu je nyní dostupná také v sadě Visual Studio 2013.

### <a name="known-issues"></a>Známé problémy
Instalace Azure SDK 2.6 nebo 2.7.1 for Visual Studio Community 2013 na není anglické operační systém zobrazí upozornění, že prostředky angličtinu a jiné než anglické jazykové sady Visual Studio se pravděpodobně neshodují. Toto upozornění může být bezpečně zrušit. Tato hodnota se vrátí pouze pokud se počítač neobsahoval předchozí instalace sady Visual Studio Community 2013 a při instalaci sady SDK na není anglické operační systém. Upozornění se zobrazí po této jazykové sady se vztahuje na Visual Studio RTM prostředky, ale před platil Update 4. Zavírání upozornění umožňuje této jazykové sady, chcete pokračovat a dokončit, použití verze Update 4 language pack obsahu.

Projekty LightSwitch nejsou s aktualizací v této vydané verzi. Tento problém bude vyřešen v další verzi sady SDK.

## <a name="also-see"></a>Viz také
[Azure SDK 2.7.1 oznámení příspěvku](https://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 oznámení příspěvku](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Podpora a informace o vyřazení sady Azure SDK pro .NET a rozhraní API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

