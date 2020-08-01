---
title: Integrace Azure Automation Update Management s koncovým bodem Windows Configuration Manager
description: V tomto článku se dozvíte, jak nakonfigurovat Configuration Manager koncového bodu Microsoft Update Management nasadit aktualizace softwaru na klienty Správce.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 4eccd53fbf3b883d6e3ba6d2c7c80ddd4ae188af
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450157"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Integrace Update Management s koncovým bodem Windows Configuration Manager

Zákazníci, kteří investovali do služby Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, se také spoléhají na jeho sílu a splatnost při správě aktualizací softwaru jako součást cyklu správy softwarových aktualizací (SUM).

Spravované servery Windows můžete nahlásit a aktualizovat tak, že vytvoříte a předvedete předprodukční nasazení aktualizací softwaru ve Windows Endpoint Configuration Manager a získáte podrobný stav dokončených nasazení aktualizací pomocí [Update Management](update-mgmt-overview.md). Pokud používáte Configuration Manager koncového bodu Windows pro vytváření sestav dodržování předpisů aktualizací, ale ne pro správu nasazení aktualizací se servery Windows, můžete pokračovat ve vytváření sestav na koncový bod Configuration Manager, zatímco aktualizace zabezpečení se spravují pomocí Azure Automation Update Management.

>[!NOTE]
>I když Update Management podporuje posouzení aktualizací a opravy Windows serveru 2008 R2, nepodporuje klienty spravované pomocí koncového bodu Configuration Manager spuštění tohoto operačního systému.

## <a name="prerequisites"></a>Předpoklady

* Musíte mít [Azure Automation Update Management](update-mgmt-overview.md) přidat do svého účtu Automation.
* Windows servery, které jsou aktuálně spravované prostředím Windows Endpoint Configuration Manager, musí také nahlásit pracovnímu prostoru Log Analytics, který má také povolený Update Management.
* Tato funkce je povolená ve Windows Endpoint Configuration Manager aktuální větev verze 1606 a vyšší. Chcete-li integrovat koncový bod Windows Endpoint Configuration Manager nebo samostatnou primární lokalitu s protokoly Azure Monitor a importovanými kolekcemi, přečtěte si téma [připojení Configuration Manager k Azure monitor protokolů](../../azure-monitor/platform/collect-sccm.md).  
* Agenti systému Windows musí být buď nakonfigurováni pro komunikaci se serverem služby Windows Server Update Services (WSUS), nebo mít přístup k Microsoft Update, pokud neobdrží aktualizace zabezpečení z Configuration Manager koncových bodů Windows.

Způsob správy klientů hostovaných v Azure IaaS s vaším stávajícím prostředím Configuration Manager koncového bodu Windows primárně závisí na připojení, které máte mezi datacentry Azure a vaší infrastrukturou. Toto připojení má vliv na jakékoli změny v návrhu, které může být potřeba provést v infrastruktuře Windows Endpoint Configuration Manager a související náklady na podporu potřebných změn. Pokud chcete lépe pochopit, jaké aspekty plánování byste měli vyhodnotit, než budete pokračovat dál, přečtěte si [nejčastější dotazy ke Configuration Manageru v Azure](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Správa aktualizací softwaru z koncového bodu Windows Configuration Manager

Pokud budete pokračovat v správě nasazení aktualizací z Configuration Manager koncového bodu Windows, proveďte následující kroky. Azure Automation se připojí ke koncovému bodu systému Windows Configuration Manager, aby mohl použít aktualizace klientských počítačů připojených ke svému pracovnímu prostoru Log Analytics. Aktualizace obsahu je k dispozici z mezipaměti klientského počítače, jako kdyby bylo nasazení spravováno koncovým bodem Windows Configuration Manager.

1. Pomocí postupu popsaného v tématu [nasazení aktualizací softwaru](/configmgr/sum/deploy-use/deploy-software-updates)vytvořte nasazení aktualizace softwaru z lokality nejvyšší úrovně ve vaší službě Endpoint Configuration Manager v hierarchii Windows. Jediným nastavením, které se musí nakonfigurovat jinak než u standardního nasazení, je možnost **Neinstalovat softwarové aktualizace**, která řídí řízení chování balíčku pro nasazení při stahování. Toto chování se spravuje v Update Management tím, že v dalším kroku vytvoří plánované nasazení aktualizace.

2. V Azure Automation vyberte **Update Management**. Pomocí kroků popsaných v [tématu Vytvoření nasazení aktualizace](update-mgmt-deploy-updates.md#schedule-an-update-deployment) vytvořte nové nasazení a vyberte **importované skupiny** v rozevíracím seznamu **typ** a vyberte příslušnou kolekci Configuration Managerho koncového bodu Windows. Mějte na paměti následující důležité skutečnosti:

    a. Pokud je ve vybrané kolekci zařízení Configuration Manager Windows Endpoint definovaný časový interval pro správu a údržbu, místo nastavení **Trvání** definovaného v naplánovaném nasazení se budou členy kolekce akceptovat.

    b. Členové cílové kolekce musí mít připojení k Internetu (buď přímo prostřednictvím proxy server, nebo prostřednictvím brány Log Analytics).

Po dokončení nasazení aktualizace prostřednictvím Azure Automation budou cílové počítače, které jsou členy vybrané skupiny počítačů, instalovat aktualizace v naplánovaném čase z místní mezipaměti klienta. Můžete si [zobrazit stav nasazení aktualizací](update-mgmt-deploy-updates.md#check-deployment-status) a monitorovat výsledky nasazení.

## <a name="manage-software-updates-from-azure-automation"></a>Správa aktualizací softwaru z Azure Automation

Pokud chcete spravovat aktualizace pro virtuální počítače s Windows serverem, které jsou klienty Windows Endpoint Configuration Manager, je potřeba nakonfigurovat zásady klienta, aby se zakázala funkce Update Management softwaru pro všechny klienty spravované přes Update Management. Nastavení klienta standardně cílí na všechna zařízení v hierarchii. Další informace o nastavení této zásady a o tom, jak je nakonfigurovat, najdete [v tématu Postup konfigurace nastavení klienta v Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

Po provedení této změny konfigurace vytvoříte nové nasazení podle kroků popsaných v [tématu Vytvoření nasazení aktualizace](update-mgmt-deploy-updates.md#schedule-an-update-deployment) a výběrem části **importované skupiny** v rozevíracím seznamu **typ** vyberte příslušnou kolekci Configuration Managerho koncového bodu Windows.

## <a name="next-steps"></a>Další kroky

Postup nastavení plánu integrace najdete v tématu [Naplánování nasazení aktualizace](update-mgmt-deploy-updates.md#schedule-an-update-deployment).
