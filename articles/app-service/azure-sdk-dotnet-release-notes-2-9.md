---
title: Poznámky k verzi sady Azure SDK pro .NET 2.9
description: Poznámky k verzi sady Azure SDK pro .NET 2.9
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
ms.openlocfilehash: 30013c0bd8ded31ccaf3cdc142bbf8dc6b375031
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Poznámky k verzi sady Azure SDK pro .NET 2.9

Toto téma obsahuje poznámky k verzi pro verze 2.9 a 2.9.6 sady Azure SDK pro .NET.

## <a name="azure-sdk-for-net-296-release-summary"></a>Azure SDK pro .NET 2.9.6 verze souhrn

Datum vydání: 11/16/2016
 
V této verzi byly zavedeny žádné nejnovější změny do Azure SDK 2.9. Je také žádné procesu upgradu potřeby využít tuto sadu SDK s existující projekty cloudové služby.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate

- V sadě Visual Studio 2017 RC tato verze sady Azure SDK pro .NET je součástí pracovního vytížení Azure. Všechny nástroje, které musíte udělat Azure development budou součástí Visual Studio RC 2017 do budoucna. Pro Visual Studio 2015 a Visual Studio 2013 sadu SDK bude stále k dispozici prostřednictvím WebPI. Již Azure SDK pro .NET verze pro sadu Visual Studio 2013, když Visual Studio 2017 uvolní jako poslední produktu. Chcete-li stáhnout sadu Visual Studio RC 2017 na tento odkaz: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnostika Azure

- Změnit chování pouze ukládat částečné připojovací řetězec s klíčem nahrazuje token pro připojovací řetězec úložiště diagnostiky cloudové služby. Klíč skutečné úložiště jsou teď uložená v složce profilu uživatele, přístup se dá řídit. Visual Studio bude číst ze složky profilu uživatele pro místní ladění a proces publikování klíče úložiště. 
- V reakci na změnu popsané výše Visual Studio Online team rozšířené šablony úloh nasazení Azure Cloud Services, mohou uživatelé zadat klíč úložiště pro nastavení rozšíření diagnostiky při publikování do Azure na průběžnou integraci a nasazení.
- Jsme provedli jsme je umožněno ukládání zabezpečené připojovací řetězec a tokenizaci pro Azure Diagnostics (WAD), které vám pomůžou vyřešit problémy s konfigurací napříč environements.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 virtuální počítače

- Visual Studio teď podporuje nasazení cloudové služby na virtuální počítače operačního systému rodiny 5 (Windows Server 2016). Pro existující cloudové služby můžete změnit nastavení pro nové řada operačního systému. Při vytváření nových cloudových služeb, pokud zvolíte možnost vytvoření služby pomocí rozhraní .net 4.6 nebo vyšší, bude použita výchozí služba používají operačního systému rodiny 5.  Další informace najdete [skupina hostovaných operačních systémů podporují tabulky](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Známé problémy

- Azure .NET SDK 2.9.6 zavedená omezení, které blokuje nasazení projektů pomocí nepodporované rozhraní .NET Framework (například .NET 4.6) do všech operačního systému rodiny < 5. Alternativní řešení [zde](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Mezipaměť hostovaná v instanci Role na Azure 

- Podpora pro Azure Cache v roli končí na 30. listopadu 2016. Další podrobnosti získáte kliknutím na [zde](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Šablony Azure Resource Manageru pro Azure zásobníku

- Zavedli jsme zásobník Azure jako cíl nasazení pro vaše šablony Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Azure SDK pro .NET 2.9 souhrn

## <a name="overview"></a>Přehled
Tento dokument obsahuje poznámky k verzi sady Azure SDK pro .NET 2.9 verze. 

Podrobné informace o aktualizacích v této verzi najdete v tématu [sadu Azure SDK 2.9 oznámení post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Azure SDK 2.9 pro Visual Studio 2015 Update 2 a Visual Studio "15" Preview
Tato aktualizace zahrnuje následující opravy chyb:

* Problém související s REST API generování klienta v, ve kterém se zobrazí řetězec "Neznámý typ" jako název složky generace kód nebo název oboru názvů vyřadit do generovaného kódu.
* Problém týkající se naplánované WebJobs, ve kterém ověřovacích informací došlo k selhání mají být předány Plánovač procesu zřizování.

Tato aktualizace zahrnuje následující nové funkce:

* Podpora pro sekundární aplikační služby na kartě "Služby" dialogové okno zřizování App Service. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Nástroje Azure Data Lake pro Visual Studio 2015 Update 2
Tato aktualizace zahrnuje následující položky:

* **Nástroje Azure Data Lake** pro Visual Studio je nyní sloučeny do Azure SDK pro .NET verze. Tento nástroj se automaticky nainstaluje při instalaci sady Azure SDK. 
  
    Tento nástroj se často aktualizuje, přejděte [sem](http://aka.ms/datalaketool) získat aktualizace.
* **V Průzkumníku serveru** teď umožňuje zobrazit všechny a vytvořit některé entity metadata U-SQL. Další informace najdete v tématu [to](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogu.

## <a name="hdinsight-tools"></a>Nástroje HDInsight
**Nástroje HDInsight** pro sadu Visual Studio teď podporuje HDInsight verze 3.3, včetně grafech Tez a dalších jazyků opravy.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Tato verze přidává [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) podporu pro šablony Resource Manageru.

## <a name="see-also"></a>Další informace najdete v tématech
[Azure SDK 2.9 oznámení post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

