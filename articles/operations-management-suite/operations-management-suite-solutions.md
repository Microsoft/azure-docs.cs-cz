---
title: Řešení pro správu Azure | Microsoft Docs
description: Řešení pro správu zahrnují scénářů zabalené správy v Azure, které zákazníci mohou přidat jejich pracovní prostor analýzy protokolů.  Tento článek poskytuje podrobné informace o tom, jak vlastní řešení vytvořené zákazníci a partneři.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394251"
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Práce s řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro řešení pro správu v Azure, které jsou aktuálně ve verzi preview.    
> 
> 

Řešení pro správu zahrnují scénářů zabalené správy, které zákazníci mohou přidat do prostředí Azure.  Kromě [řešení od společnosti Microsoft](../log-analytics/log-analytics-add-solutions.md), partnery a zákazníky, můžete vytvořit řešení pro správu k použití ve svém vlastním prostředí nebo přístupná zákazníkům prostřednictvím komunitou.

## <a name="finding-and-installing-management-solutions"></a>Hledání a instalace řešení pro správu
Existuje více metod pro vyhledání a instalace řešení pro správu, jak je popsáno v následujících částech.

### <a name="azure-marketplace"></a>Azure Marketplace
Řešení pro správu od společnosti Microsoft a důvěryhodným partnerům může být nainstalována v Azure Marketplace na portálu Azure.

1. Přihlaste se k webu Azure Portal.
2. V levém podokně vyberte **všechny služby**.
3. Buď přejděte dolů k položce **řešení** nebo typ *řešení* do **filtru** dialogové okno.
4. Klikněte **+ přidat** tlačítko.
5. Vyhledejte řešení, která vás zajímá buď procházením, kliknutím na **filtru** tlačítko nebo zadáním v **vyhledávání Everthing** pole.
6. Klikněte na položku marketplace zobrazíte její podrobné informace.
7. Klikněte na tlačítko **vytvořit** otevřete **přidat řešení** podokně.
8. Zobrazí se výzva k požadované informace, jako [pracovní prostor analýzy protokolů a účet Automation](#log-analytics-workspace-and-automation-account) kromě hodnoty všech parametrů v řešení.
9. Klikněte na tlačítko **vytvořit** instalací řešení.

### <a name="oms-portal"></a>Portálu OMS
Řešení pro správu od společnosti Microsoft může být nainstalována z Galerie řešení na portálu OMS.

1. Přihlaste se k portálu OMS.
2. Klikněte **řešení Galerie** dlaždici.
3. Na stránce Galerie řešení OMS informace o jednotlivých k dispozici řešení. Klikněte na název řešení, které chcete přidat.
4. Na stránce pro řešení, které jste vybrali zobrazí se podrobné informace o řešení. Klikněte na tlačítko **Add** (Přidat).
5. Nová dlaždice pro řešení, které jste přidali, zobrazí se na přehled stránku v portálu a můžete jej začít používat po analýzy protokolů zpracovává vaše data.

### <a name="azure-quickstart-templates"></a>Rychlý úvod do šablon pro Azure
Členové komunity můžete odeslat řešení pro správu šablon Azure rychlý start.  Můžete stahovat tyto šablony pro pozdější instalaci nebo je další zkontrolovat postup [vytvářet vlastní řešení](#creating-a-solution).

1. Postupujte podle procesu popsaného v tématu [pracovní prostor analýzy protokolů a účet Automation](#log-analytics-workspace-and-automation-account) propojit pracovní prostor a účet.
2. Přejděte na [šablony Azure rychlý Start](https://azure.microsoft.com/documentation/templates/).  
3. Hledání řešení, které vás zajímají.
4. Vyberte řešení z výsledků zobrazíte její podrobnosti.
5. Klikněte **nasadit do Azure** tlačítko.
6. Zobrazí se výzva k poskytují informace, jako je skupina prostředků a umístění kromě hodnoty všech parametrů v řešení.
7. Klikněte na tlačítko **nákupu** instalací řešení.

### <a name="deploy-azure-resource-manager-template"></a>Nasazení šablony Azure Resource Manageru
Řešení, které jste získali od komunity nebo [vytvořit sami](#creating-a-solution) jsou implementované jako šablony Resource Manageru, a můžete použít libovolnou metodu standardní pro [nasazení šablony](../azure-resource-manager/resource-group-template-deploy-portal.md).  Všimněte si, že před instalací řešení, musíte vytvořit a odkaz [pracovní prostor analýzy protokolů a účet Automation](#log-analytics-workspace-and-automation-account).

## <a name="log-analytics-workspace-and-automation-account"></a>Pracovní prostor analýzy protokolů a účet Automation.
Většina řešení pro správu vyžadují [pracovní prostor analýzy protokolů](../log-analytics/log-analytics-manage-access.md) tak, aby obsahovala zobrazení a [účet Automation](../automation/automation-security-overview.md#automation-account-overview) tak, aby obsahovala sady runbook a související prostředky. Pracovní prostor a účet, musí splňovat následující požadavky.

* Řešení lze použít pouze jeden pracovní prostor analýzy protokolů a jeden účet Automation.  
* Pracovní prostor analýzy protokolů a účet Automation používá řešení musí být propojena na sebe navzájem. Pracovní prostor analýzy protokolů může propojit jen s jeden účet Automation a účet Automation může propojit jen do jednoho pracovního prostoru analýzy protokolů.
* Propojení, pracovní prostor analýzy protokolů a účet Automation musí být ve stejné skupině prostředků a oblast.  Výjimkou je pracovního prostoru v oblasti Východ USA a a účet Automation v oblasti Východ USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Vytvoření propojení mezi pracovní prostor analýzy protokolů a účet Automation.
Jak zadejte pracovní prostor analýzy protokolů a účet Automation, závisí na metodě instalace pro vaše řešení.

* Při instalaci řešení společnosti Microsoft prostřednictvím portálu OMS je nainstalován v aktuálním pracovním prostoru a není třeba žádný účet Automation.
* Když instalujete řešení prostřednictvím Azure Marketplace, budete vyzváni k pracovní prostor a účet Automation a se vám vytvoří propojení mezi nimi.  
* Pro řešení mimo Azure Marketplace je nutné před instalací řešení propojit pracovní prostor analýzy protokolů a účet Automation.  Můžete provést zvolením řešení v Azure Marketplace a vyberete pracovní prostor analýzy protokolů a účet Automation.  Nemáte skutečně nainstalovat řešení, protože odkaz se vytvoří, jakmile jsou vybrané pracovní prostor analýzy protokolů a účet Automation.  Po vytvoření odkazu, tento pracovní prostor analýzy protokolů a účet Automation můžete použít pro žádné řešení. 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi pracovní prostor analýzy protokolů a účet Automation.
Můžete ověřit propojení mezi pracovní prostor analýzy protokolů a účtu Automation pomocí následujícího postupu.

1. Vyberte účet Automation na portálu Azure.
2. Pokud **prostoru** nastavení v **související prostředky** části nabídky je zapnutý, pak tento účet je připojen k pracovní prostor analýzy protokolů.  Kliknutím na **prostoru** k zobrazení podrobností o pracovním prostoru.

## <a name="listing-management-solutions"></a>Výpis řešení pro správu
Chcete-li zobrazit řešení pro správu v pracovní prostory přidružený k vašemu předplatnému Azure se používá následující postup.

1. Přihlaste se k webu Azure Portal.
2. V levém podokně vyberte **všechny služby**.
3. Buď přejděte dolů k položce **řešení** nebo typ *řešení* do **filtru** dialogové okno.
4. Objeví se řešení, které jsou nainstalovány ve všech vašich pracovních prostorů.

Všimněte si, že se zobrazí pouze řešení společnosti Microsoft nainstalované v aktuálním pracovním prostoru pomocí portálu OMS.

## <a name="removing-a-management-solution"></a>Odebrání řešení pro správu
Pokud je odebrán řešení pro správu, budou odebrány také všechny prostředky v řešení.  

1. Vyhledejte řešení na portálu Azure pomocí postupu v [výpis řešení](#listing-solutions).
2. Vyberte řešení, které chcete odebrat.
3. Klikněte **odstranit** tlačítko.

## <a name="creating-a-management-solution"></a>Vytváření řešení pro správu
Úplné pokyny k vytváření řešení pro správu jsou k dispozici na [vytváření řešení v Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Další postup
* Hledání [šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates) ukázky různých šablonách Resource Manager.
* Vytvořit vlastní [řešení pro správu](operations-management-suite-solutions-creating.md).

