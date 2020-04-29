---
title: Použití Azure Automation Update Management s klienty Configuration Manager
description: Tento článek je určený k tomu, abyste vám pomohli nakonfigurovat Configuration Manager koncového bodu Microsoft pomocí tohoto řešení k nasazení aktualizací softwaru do klientů nástroje ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 32a077c476d9669c3f32bd4040fdc8ff90156c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678735"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Nasazení aktualizací do klientů Microsoft Endpoint Configuration Manager pomocí Update Management

Zákazníci, kteří investovali do služby Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, se také spoléhají na jeho sílu a splatnost při správě aktualizací softwaru jako součást cyklu správy softwarových aktualizací (SUM).

Spravované servery Windows můžete ohlásit a aktualizovat tak, že vytvoříte a předvedete nasazení aktualizací softwaru v nástroji Configuration Manager a získáte podrobný stav dokončených nasazení aktualizací pomocí [Update Management](automation-update-management.md). Pokud používáte Configuration Manager pro vytváření sestav o dodržování předpisů aktualizací, ale ne pro správu nasazení aktualizací se servery Windows, můžete pokračovat ve vytváření sestav pro Configuration Manager, zatímco se aktualizace zabezpečení spravují pomocí řešení Update Management.

## <a name="prerequisites"></a>Požadavky

* Musíte mít přidané [řešení Update Management](automation-update-management.md) do svého účtu Automation.
* Windows servery, které jsou aktuálně spravované prostředím Configuration Manager, musí také nahlásit pracovnímu prostoru Log Analytics, který má povolené řešení Update Management.
* Tato funkce je povolená v Configuration Manager aktuální větev verze 1606 a vyšší. Pokud chcete integrovat Configuration Manager lokalitu centrální správy nebo samostatnou primární lokalitu s protokoly Azure Monitor a importovanými kolekcemi, přečtěte si téma [připojení Configuration Manager k Azure monitor protokolů](../azure-monitor/platform/collect-sccm.md).  
* Pokud agenti Windows nedostávají aktualizace zabezpečení z Configuration Manageru, musí mít nakonfigurovanou komunikaci se serverem služeb Windows Server Update Services (WSUS) nebo musí mít přístup ke službě Microsoft Update.   

To, jak spravujete klienty hostované v Azure IaaS s využitím stávajícího prostředí Configuration Manageru, závisí primárně na připojení, které máte mezi datovými centry Azure a vaší infrastrukturou. Toto připojení ovlivňuje případné změny v návrhu, které je třeba provést v infrastruktuře Configuration Manageru, a náklady související s provedením těchto nezbytných změn. Pokud chcete lépe pochopit, jaké aspekty plánování byste měli vyhodnotit, než budete pokračovat dál, přečtěte si [nejčastější dotazy ke Configuration Manageru v Azure](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurace

### <a name="manage-software-updates-from-configuration-manager"></a>Správa aktualizací softwaru s využitím Configuration Manageru 

Pokud budete nadále spravovat nasazení aktualizace z Configuration Manageru, proveďte následující kroky. Azure Automation se připojí k Configuration Manager a použije aktualizace klientských počítačů, které jsou připojené ke svému pracovnímu prostoru Log Analytics. Obsah aktualizace obsah je dostupný z mezipaměti klientského počítače, stejně jako v případě, kdy nasazení spravoval Configuration Manager.

1. Pomocí procesu popsaného v části [nasazení aktualizací softwaru](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)vytvořte nasazení aktualizace softwaru z lokality nejvyšší úrovně ve vaší Configuration Manager hierarchii. Jediným nastavením, které se musí nakonfigurovat jinak než u standardního nasazení, je možnost **Neinstalovat softwarové aktualizace**, která řídí řízení chování balíčku pro nasazení při stahování. Toto chování spravuje řešení Update Management tím, že v dalším kroku vytvoří plánované nasazení aktualizace.

1. V Azure Automation vyberte **Update Management**. Pomocí kroků popsaných v [tématu Vytvoření nasazení aktualizace](automation-tutorial-update-management.md#schedule-an-update-deployment) vytvořte nové nasazení a vyberte **importované skupiny** v rozevíracím seznamu **typ** a vyberte příslušnou kolekci Configuration Manager. Pamatujte na následující důležité body: a. Pokud je pro vybranou Configuration Manager kolekci zařízení definované časové období údržby, budou se členové kolekce akceptovat místo nastavení **Trvání** definovaného v naplánovaném nasazení.
    b. Členové cílové kolekce musí mít připojení k Internetu (buď přímo prostřednictvím proxy server, nebo prostřednictvím brány Log Analytics).

Po dokončení nasazení aktualizace prostřednictvím Azure Automation budou cílové počítače, které jsou členy vybrané skupiny počítačů, instalovat aktualizace v naplánovaném čase z místní mezipaměti klienta. Můžete si [zobrazit stav nasazení aktualizací](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a monitorovat výsledky nasazení.

### <a name="manage-software-updates-from-azure-automation"></a>Správa aktualizací softwaru z Azure Automation

Pokud chcete spravovat aktualizace pro virtuální počítače s Windows Serverem, které jsou klienty Configuration Manageru, musíte nakonfigurovat zásady klienta tak, aby funkce správy softwarových aktualizací byla zakázaná pro všechny klienty spravované tímto řešením. Nastavení klienta standardně cílí na všechna zařízení v hierarchii. Další informace o nastavení této zásady a o tom, jak je nakonfigurovat, najdete [v tématu Postup konfigurace nastavení klienta v Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Po provedení této změny konfigurace vytvoříte nové nasazení podle kroků popsaných v [tématu Vytvoření nasazení aktualizace](automation-tutorial-update-management.md#schedule-an-update-deployment) a vybráním položku **importované skupiny** v rozevíracím seznamu **typ** vyberte příslušnou kolekci Configuration Manager.

## <a name="next-steps"></a>Další kroky

