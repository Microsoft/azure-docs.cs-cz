---
title: Poznámky k verzi sady Azure SDK pro .NET 2.7 a .NET 2.7.1
description: Poznámky k verzi sady Azure SDK pro .NET 2.7 a .NET 2.7.1
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
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836718"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Poznámky k verzi sady Azure SDK pro .NET 2.7 a .NET 2.7.1
## <a name="overview"></a>Přehled
Tento dokument obsahuje poznámky k verzi sady Azure SDK pro .NET 2.7 verze. 

Dokument také obsahovat poznámky k verzi pro sadu Azure SDK pro .NET 2.7.1 verzi.

Azure SDK 2.7 je podporována pouze v sadě Visual Studio 2015 a Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) je poslední nepodporují SDK pro sadu Visual Studio 2012.

Podrobné informace o tomto vydání najdete v tématu [post oznámení Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) a [post oznámení Azure SDK 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK pro .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Přihlaste se vylepšení pro Visual Studio 2015
2.7 Azure SDK pro Visual Studio 2015 podporuje nové funkce správy identit v sadě Visual Studio 2015.  To zahrnuje podporu pro účty přístupu k Azure prostřednictvím řízení přístupu na základě rolí, poskytovatele cloudových řešení, DreamSpark a dalších typů účet a předplatné.

Vylepšení přihlášení součástí Azure SDK 2.7 jsou pouze k dispozici v sadě Visual Studio 2015. Podpora pro Visual Studio 2013 je součástí sady Azure SDK 2.7.1.

### <a name="mobile-sdk"></a>Mobilní SDK
Aktualizovat **Mobile Apps** šablony tak, aby odrážela nejnovější [balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) a instalační program procesu.

### <a name="service-bus"></a>Service Bus
Obecné oprav chyb a vylepšení. Podrobnosti o aktualizacemi a funkcemi, naleznete v poznámkách k verzi z nejnovější [Service Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>Nástroje HDInsight
V této verzi provedeny následující aktualizace. Tyto aktualizace jsou ve verzi preview. Další informace najdete v tématu [tomto blogu](http://go.microsoft.com/fwlink/?LinkId=619108).

* Hive grafy pro Hive na úlohách Tez
* Plná podpora Hive DML IntelliSense
* Podpora šablony pig
* Storm šablon pro služby Azure

#### <a name="breaking-changes"></a>Nejnovější změny
* Původní **Storm** při použití této verzi nástroje, musí být upgradován projektu. Další informace najdete v tématu [tomto blogu](http://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express již není podporována. Další informace najdete v tématu [tomto blogu](http://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Nástroje služby Azure App Service
V této verzi byly provedeny následující aktualizace pro rozšíření nástrojů Web. Další informace najdete v části [to](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogu. 

* Podpora pro DreamSpark účty přidat
* Úplné do nástroje Azure změny pro podporu nových rozhraní API správy prostředků Azure
* Podpora pro Azure App Service přidat do [Průzkumníku cloudu](#cloud_explorer)

#### <a name="known-issues"></a>Známé problémy
Uzly slotu nasazení aplikace Web nezobrazí v uzlu sloty v Průzkumníku serveru a uzly podřízené slotu nasazení webové aplikace se nenačtou v Průzkumníku cloudu. Tento problém byl vyřešen a připravit pro další verze sady SDK. 

### <a name="cloud_explorer"></a>Průzkumník cloudu pro Visual Studio 2015
Azure SDK 2.7 zahrnuje Průzkumník cloudu pro Visual Studio 2015, která umožňuje zobrazení vašich prostředků Azure, zkontrolujte jejich vlastnosti a provádět akce klíče vývojáře z Visual Studia. 

Průzkumník cloudu podporuje následující funkce:

* Zobrazení skupiny prostředků a typ prostředku vašich prostředků Azure 
* Hledat prostředky podle názvu (k dispozici v zobrazení typ prostředku)
* Podpora pro odběry a prostředky, které mají na základě řízení přístupu Role (RBAC) použít 
* Integrované panelu akcí, které znázorňuje zaměřené na vývojáře akce specifické pro vybrané prostředky. Příklad: připojení vzdáleného ladicího programu pro virtuální počítače vytvořené pomocí do zásobníku Resource Manager, zobrazení dat diagnostiky pro virtuální počítače atd.
* Integrované panely vlastnosti, které se zobrazují vlastnosti zaměřené na vývojáře běžně potřebný během vývoje/testování 
* Rychlé přepínání účet, který chcete použít při vytváření výčtu zdroje (použijte příkaz Nastavení na panelu nástrojů) 
* Filtrování odběrů použít při vytváření výčtu zdroje (použijte příkaz Nastavení na panelu nástrojů) 
* Přímé odkazy na portálu Azure ke správě prostředků a skupin prostředků 

### <a name="azure-resource-manager-tools"></a>Nástroje Azure Resource Manager
Pro práci s na základě řízení přístupu Role (RBAC) a nové typy předplatné bylo aktualizováno nástroje Správce prostředků Azure.  Součástí těchto změn je schopnost používat nové účty úložiště, kromě classic úložiště k ukládání artefaktů během nasazení.  

Pokud používáte projekt skupiny prostředků Azure z předchozí verze sady SDK s SDK 2.7, nový skript nasazení je potřeba k nasazení pomocí nového účtu úložiště místo klasického úložiště.  Zobrazí se výzva před provedením změn do projektu přidat nový skript.  Původní skript se přejmenuje a budete muset ručně Zkontrolujte veškeré úpravy nový skript.

### <a name="storage-explorer-tools"></a>Nástroje pro Průzkumníka úložiště
* Podpora pro zobrazení doplňovacích objektů BLOB. Další informace v [tomto příspěvku na blogu](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Podpora pro zobrazení účty služby Premium Storage pomocí Průzkumníka serveru. Průzkumníku serveru se zobrazí jenom objekty BLOB stránky pro účty úložiště premium, protože se jedná o podporuje jen typ pro účty úložiště premium.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Azure Data Factory Tools for Visual Studio
Představení **nástrojů Azure Data Factory** pro sadu Visual Studio. Níže jsou povolené funkce. V tématu [tomto blogu](http://go.microsoft.com/fwlink/?LinkId=617530) Další informace.

* **Na základě šablony při vytváření**: Vyberte použití použita na základě šablony, šablony pro přesun dat nebo zpracování dat šablony nasadit integrační řešení začátku do konce dat a začít praktických rychle pomocí služby Data Factory. 
* **Integrace s Průzkumníku řešení pro vytváření a nasazení entit služby Data Factory**: vytvoření a nasazení kanálů a entit v relaci jako projektů sady Visual Studio. 
* **Integrace s zobrazení diagramu pro visual interakce při vytváření**: vizuálně vytváření kanálů a datové sady s podporou v zobrazení diagramu. 
* **Integrace s Průzkumníka serveru pro procházení a interakci s již nasazené entity**: využít Průzkumníka serveru pro procházení nasazené datové továrny a odpovídající entity. Importujte objekt pro vytváření dat nasazené nebo entitu (kanál, propojené služby, datové sady) do projektu. 
* **JSON úpravy pomocí schématu ověření a bohaté intellisense**: efektivní konfigurace a úpravám dokumentů JSON entit služby Data Factory s bohaté funkce intellisense a schéma ověřování 
* **Publikování více prostředí**: publikování vytvořené kanály pro vývojáře, testu nebo produkčnímu prostředí tak, že vytvoříte samostatné konfigurační soubory pro každé prostředí.
* **Pig, Hive a .net podporuje zpracování dat na základě**: podpora pro Pig a Hive skripty v projekt Data Factory. Podpora pro odkazování na projekt C# pro správu .net aktivity.

## <a name="azure-sdk-for-net-271"></a>Azure SDK pro .NET 2.7.1
Následující oddíl obsahuje aktualizace, které byly zavedeny v Azure SDK pro .NET 2.7.1 verzi.

### <a name="hdinsight-tools"></a>Nástroje HDInsight
Podrobné vysvětlení o aktualizacích nástroje HDInsight, najdete v části [tomto blogu](http://go.microsoft.com/fwlink/?LinkId=623831).

* Operátor zobrazení úloh Hive (nová funkce)
  
    Vám pomůžou pochopit dotaz Hive lépe, zobrazení operátora Hive funkci se přidala. Pokud chcete zobrazit všechny operátory uvnitř vrcholu, dvakrát klikněte na vrcholy grafu úlohy. Chcete-li zobrazit podrobnosti o konkrétní operátor, pozastavte ukazatel myši nad tento operátor.
* Hive chyby značky (nová funkce)
  
    Umožnit zobrazení chyb gramatika okamžitě, funkci Hive značky chyba byla přidána. Navíc byly vylepšeny chybové zprávy a můžete nyní zobrazit podrobné gramatika chyby okamžitě (až do této verze, museli jste odeslat skript Hive do clusteru a počkejte chvíli před získáním podrobnosti o chybová zpráva).  
* Graf topologie Storm (nová funkce)
  
    Vizualizace je velmi důležité, pokud chcete zobrazit, pokud vaše topologie funguje podle očekávání. V této verzi jsme přidali vizualizaci Storm grafy. Můžete vizualizovat důležitá metriky pro topologii (například barvu, která označuje počasí na určité funkce Bolt "zaneprázdněn" nebo ne). Můžete také dvakrát kliknete Spout nebo Bolt zobrazit další podrobnosti.
* Podpora pro clustery HDInsight, které byly vytvořeny na portálu Azure (oprava chyb)
  
    Visual Studio teď slouží k zobrazení a odesílání úloh pro všechny clustery HDInsight bez ohledu na to, kde byly vytvořeny clusteru.
* Další podporu technologie IntelliSense & rychlejší Hive Metadata načítání (zlepšení)
  
    IntelliSense jsme vylepšili přidáním další návrhy uživatelsky přívětivý. Například tabulky alias můžete nyní také navrženo v technologii IntelliSense, lze snadněji napsat dotaz. Také jsme vylepšili metadata Hivu načítání tak bude trvat jenom několik sekund k zobrazení seznamu všech databází, tabulky a sloupce vaše metaúložiště Hive.

Podrobné vysvětlení o aktualizacích nástroje HDInsight, najdete v části [tomto blogu](http://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Vylepšení v sadě Visual Studio 2013
* Azure SDK 2.7.1 umožňuje Visual Studio 2013 pro přístup k Azure účtům a předplatným prostřednictvím řízení přístupu na základě rolí, poskytovatele cloudových řešení a Dreamspark.
* Sada Azure SDK 2.7.1 a nové okno nástroje Průzkumník cloudu je nyní dostupná také v sadě Visual Studio 2013.

### <a name="known-issues"></a>Známé problémy
Instalace Azure SDK 2.6 nebo 2.7.1 pro Visual Studio Community 2013 na jiný - anglické operačního systému se zobrazí upozornění, může být neshoda angličtinu a jiných než anglických prostředky sady Visual Studio. Toto upozornění může bezpečně zrušit. Se vztahuje pouze pokud je počítač neobsahoval předchozí instalace Visual Studio Community 2013 a instalujete sadu SDK na jiný - anglické operačního systému. Toto upozornění se zobrazí po jazyková sada platí pro Visual Studio RTM prostředky, ale předtím, než se vztahuje aktualizací 4. Zavření upozornění vám umožní jazyková sada pokračovat a dokončete použití aktualizace 4 verzi obsahu language pack.

Projekty LightSwitch nejsou s aktualizací v této verzi. Tento problém bude vyřešen další verze sady SDK.

## <a name="also-see"></a>Viz také
[Azure SDK 2.7.1 post oznámení](http://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 oznámení post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Podporu a informace o vyřazení pro sadu Azure SDK pro .NET a rozhraní API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

