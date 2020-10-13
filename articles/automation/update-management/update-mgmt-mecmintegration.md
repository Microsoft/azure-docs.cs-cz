---
title: Integrace Azure Automation Update Management s koncovým bodem Microsoft Configuration Manager
description: V tomto článku se dozvíte, jak nakonfigurovat Configuration Manager koncového bodu Microsoft Update Management nasadit aktualizace softwaru na klienty Správce.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 09c8ef818428157c7de8c3a87bcce8a7b80e62ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88245906"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Integrace Update Management s koncovým bodem Microsoft Configuration Manager

Zákazníci, kteří investovali do služby Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, se také spoléhají na jeho sílu a splatnost při správě aktualizací softwaru jako součást cyklu správy softwarových aktualizací (SUM).

Spravované servery Windows můžete nahlásit a aktualizovat tak, že vytvoříte a předvedete předprodukční nasazení aktualizací softwaru ve Configuration Manager koncového bodu Microsoftu a získáte podrobný stav dokončených nasazení aktualizací pomocí [Update Management](update-mgmt-overview.md). Pokud používáte službu Microsoft Endpoint Configuration Manager pro vytváření sestav dodržování předpisů, ale ne pro správu nasazení aktualizací se servery Windows, můžete pokračovat ve vytváření sestav do služby Microsoft Endpoint Configuration Manager, zatímco aktualizace zabezpečení jsou spravované pomocí Azure Automation Update Management.

>[!NOTE]
>I když Update Management podporuje posouzení aktualizací a opravy Windows serveru 2008 R2, nepodporuje klienty spravované koncovým bodem Microsoft Configuration Manager spuštění tohoto operačního systému.

## <a name="prerequisites"></a>Požadavky

* Musíte mít [Azure Automation Update Management](update-mgmt-overview.md) přidat do svého účtu Automation.
* Windows servery, které aktuálně spravuje prostředí Microsoft Endpoint Configuration Manager, musí také nahlásit pracovnímu prostoru Log Analytics, který má také povolený Update Management.
* Tato funkce je povolená v Microsoft Endpoint Configuration Manager aktuální větev verze 1606 a vyšší. Chcete-li integrovat službu Microsoft Endpoint Configuration Manager lokalitu centrální správy nebo samostatnou primární lokalitu s protokoly Azure Monitor a importovanými kolekcemi, přečtěte si téma [připojení Configuration Manager a protokoly Azure monitor](../../azure-monitor/platform/collect-sccm.md).  
* Agenti systému Windows musí být buď nakonfigurováni pro komunikaci se serverem služby Windows Server Update Services (WSUS), nebo mít přístup k Microsoft Update, pokud neobdrží aktualizace zabezpečení od služby Microsoft Endpoint Configuration Manager.

Jak spravovat klienty hostované v Azure IaaS s existujícím prostředím Microsoft Endpoint Configuration Manager primárně závisí na připojení, které máte mezi datacentry Azure a vaší infrastrukturou. Toto připojení má vliv na změny v návrhu, které může být nutné provést v infrastruktuře služby Microsoft Endpoint Configuration Manager a související náklady na podporu potřebných změn. Pokud chcete lépe pochopit, jaké aspekty plánování byste měli vyhodnotit, než budete pokračovat dál, přečtěte si [nejčastější dotazy ke Configuration Manageru v Azure](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Správa aktualizací softwaru z koncového bodu Microsoft Configuration Manager

Pokud budete pokračovat v správě nasazení aktualizací z koncového bodu Microsoft Configuration Manager, proveďte následující kroky. Azure Automation se připojí ke službě Microsoft Endpoint Configuration Manager, aby se aktualizace projevily u klientských počítačů připojených ke svému pracovnímu prostoru Log Analytics. Aktualizace obsahu je k dispozici z mezipaměti klientského počítače, jako kdyby bylo nasazení spravováno koncovým bodem Microsoft Configuration Manager.

1. Pomocí postupu popsaného v tématu [nasazení aktualizací softwaru](/configmgr/sum/deploy-use/deploy-software-updates)vytvořte nasazení aktualizace softwaru z lokality nejvyšší úrovně ve vaší hierarchii služby Microsoft Endpoint Configuration Manager. Jediným nastavením, které se musí nakonfigurovat jinak než u standardního nasazení, je možnost **Neinstalovat softwarové aktualizace**, která řídí řízení chování balíčku pro nasazení při stahování. Toto chování se spravuje v Update Management tím, že v dalším kroku vytvoří plánované nasazení aktualizace.

2. V Azure Automation vyberte **Update Management**. Pomocí kroků popsaných v [tématu Vytvoření nasazení aktualizace](update-mgmt-deploy-updates.md#schedule-an-update-deployment) vytvořte nové nasazení a vyberte **importované skupiny** v rozevíracím seznamu **typ** a vyberte příslušnou kolekci Configuration Managerho koncového bodu Microsoft. Mějte na paměti následující důležité skutečnosti:

    a. Pokud je ve vybrané kolekci zařízení Microsoft Endpoint Configuration Manager definovaný časový interval pro správu a údržbu, budou se členové kolekce akceptovat místo nastavení **Trvání** definovaného v naplánovaném nasazení.

    b. Členové cílové kolekce musí mít připojení k Internetu (buď přímo prostřednictvím proxy server, nebo prostřednictvím brány Log Analytics).

Po dokončení nasazení aktualizace prostřednictvím Azure Automation budou cílové počítače, které jsou členy vybrané skupiny počítačů, instalovat aktualizace v naplánovaném čase z místní mezipaměti klienta. Můžete si [zobrazit stav nasazení aktualizací](update-mgmt-deploy-updates.md#check-deployment-status) a monitorovat výsledky nasazení.

## <a name="manage-software-updates-from-azure-automation"></a>Správa aktualizací softwaru z Azure Automation

Pokud chcete spravovat aktualizace pro virtuální počítače s Windows serverem, které jsou klienty Microsoft Endpoint Configuration Manager, musíte nakonfigurovat zásady klienta, aby se zakázala funkce Update Management softwaru pro všechny klienty spravované přes Update Management. Nastavení klienta standardně cílí na všechna zařízení v hierarchii. Další informace o nastavení této zásady a o tom, jak je nakonfigurovat, najdete [v tématu Postup konfigurace nastavení klienta v Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

Po provedení této změny konfigurace vytvoříte nové nasazení podle kroků popsaných v [tématu Vytvoření nasazení aktualizace](update-mgmt-deploy-updates.md#schedule-an-update-deployment) a výběrem části **importované skupiny** v rozevíracím seznamu **typ** vyberte příslušnou kolekci Configuration Managerho koncového bodu Microsoft.

## <a name="next-steps"></a>Další kroky

Postup nastavení plánu integrace najdete v tématu [Naplánování nasazení aktualizace](update-mgmt-deploy-updates.md#schedule-an-update-deployment).
