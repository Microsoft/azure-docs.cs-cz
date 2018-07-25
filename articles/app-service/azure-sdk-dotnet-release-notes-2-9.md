---
title: Zpráva k vydání verze Azure SDK pro .NET 2.9
description: Zpráva k vydání verze Azure SDK pro .NET 2.9
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 01b8ccc9fe6b5469408131bce68a903f15382998
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222524"
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Poznámky k verzi Azure SDK pro .NET 2.9

Toto téma obsahuje poznámky k verzi pro verze 2.9 a 2.9.6 sady Azure SDK pro .NET.

## <a name="azure-sdk-for-net-296-release-summary"></a>Souhrn vydání Azure SDK pro .NET 2.9.6

Datum vydání: 11/16/2016
 
V této verzi byly zavedeny žádné významné změny do Azure SDK 2.9. Neexistuje žádný proces upgradu potřebné k využití této sady SDK pomocí existujících projektů cloudové služby.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate

- V sadě Visual Studio 2017 RC jsou integrované pro úlohy Azure pro tuto verzi sady Azure SDK pro .NET. Všechny nástroje, které je třeba provést vývoj pro Azure budou součástí Visual Studio 2017 RC do budoucna. Pro Visual Studio 2015 a Visual Studio 2013 SDK bude stále k dispozici prostřednictvím instalace webové platformy. Jsme se být zrušený sady Azure SDK pro .NET verze pro Visual Studio 2013, jakmile vyjde nová verze sady Visual Studio 2017 jako konečného produktu. Přejděte na tento odkaz ke stažení Visual Studio 2017 RC: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnostika Azure

- Změnit chování při pouze částečné připojovací řetězec uložit pomocí klíče, nahrazuje token pro připojovací řetězec úložiště diagnostiky cloudové služby. Klíč skutečného úložiště jsou teď uložená v složce profilu uživatele, takže je možné řídit jeho přístup. Visual Studio načte klíč úložiště ze složky profil uživatele pro místní ladění a proces publikování. 
- V reakci na změnu je popsáno výše, vylepšené týmu Visual Studio Online šablony nasazení úloh Azure Cloud Services tak, aby uživatelé mohli zadat klíč úložiště pro nastavení diagnostické rozšíření při publikování do Azure na průběžné integrace a Nasazení.
- Provedli jsme je možné ukládat zabezpečeného připojovacího řetězce a Tokenizace pro Azure Diagnostics (WAD), které vám pomůžou vyřešit problémy s konfigurací v prostředí koordinovaná.
 
### <a name="windows-server-2016-virtual-machines"></a>Virtuálních počítačů Windows serveru 2016

- Visual Studio teď podporuje nasazení Cloud Services na virtuální počítače řady 5 operačního systému (Windows Server 2016). Pro existující cloudové služby můžete změnit nastavení pro cílení nová řada operačního systému. Při vytváření nových cloudových služeb, pokud se rozhodnete pro vytvoření služby pomocí .net 4.6 nebo novější, nastaví se jako výchozí služby pro operační systém řady 5.  Další informace, můžete zkontrolovat [hostovaný operační systém řady podporují tabulky](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Známé problémy

- Azure .NET SDK 2.9.6 zavedené omezení, která blokuje nasazení projektů pomocí nepodporované rozhraní .NET Framework (například .NET 4.6) pro libovolný operační systém řady < 5. Alternativní [tady](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Mezipaměť In-Role Azure 

- Podpora pro Azure Cache In-Role končí 30. listopadu 2016. Další podrobnosti získáte kliknutím [tady](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Šablony Azure Resource Manageru pro službu Azure Stack

- Zavedli jsme Azure Stack jako cíl nasazení pro šablony Azure Resource Manageru.


## <a name="azure-sdk-for-net-29-summary"></a>Azure SDK pro .NET 2.9 souhrn

## <a name="overview"></a>Přehled
Tento dokument obsahuje zpráva k vydání verze pro sadu Azure SDK pro .NET 2.9 verzi. 

Podrobné informace o aktualizacích v této verzi najdete v tématu [sadu Azure SDK 2.9 oznámení příspěvku](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Azure SDK 2.9 pro Visual Studio 2015 Update 2 a sady Visual Studio "15" Preview
Tato aktualizace zahrnuje následující opravy:

* Problém související s REST API pro generování klientů ve kterém se zobrazí řetězec "Neznámý typ" jako název složky code-gen a/nebo název oboru názvů přetáhnout do generovaného kódu.
* Problém týkající se plánovaných WebJobs, ve kterém ověřovacích informací došlo k selhání mají být předány do zřizovacího procesu plánovače.

Tato aktualizace zahrnuje následující nové funkce:

* Podpora pro sekundární App Services na kartě "Služby" dialogové okno zřizování App Service. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake Tools pro Visual Studio 2015 Update 2
Tato aktualizace zahrnuje následující položky:

* **Azure Data Lake Tools** pro Visual Studio je nyní sloučeny do sady Azure SDK pro .NET verze. Nástroj je automaticky nainstalován při instalaci sady Azure SDK. 
  
    Nástroj se často aktualizuje, přejděte [tady](http://aka.ms/datalaketool) můžou získat aktualizace.
* **Průzkumník serveru** teď umožňuje zobrazit vše a vytvořit některé metadat entity U-SQL. Další informace najdete v tématu [to](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogu.

## <a name="hdinsight-tools"></a>Nástroje HDInsight
**Nástroje HDInsight** pro sadu Visual Studio teď podporuje opravy HDInsight verze 3.3, včetně zobrazení grafech Tez a jiný jazyk.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Tato verze přidává [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) podporu pro šablony Resource Manageru.

## <a name="see-also"></a>Další informace najdete v tématech
[Azure SDK 2.9 oznámení příspěvku](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

