---
title: Použití Azure Update Management s klienty Configuration Manager
description: Tento článek je určený k tomu, abyste vám pomohli nakonfigurovat Configuration Manager koncového bodu Microsoft pomocí tohoto řešení k nasazení aktualizací softwaru do klientů nástroje ConfigMgr.
services: automation
ms.subservice: update-management
ms.date: 03/19/2018
ms.topic: conceptual
ms.openlocfilehash: 9df401ec9c6d11bfef5d1d60833c855029f8ca01
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769944"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Nasazení aktualizací do klientů Microsoft Endpoint Configuration Manager pomocí Update Management

Zákazníci, kteří investovali do služby Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, se také spoléhají na jeho sílu a splatnost při správě aktualizací softwaru jako součást cyklu správy softwarových aktualizací (SUM).

Sestavy a aktualizovat spravované servery Windows tak, že vytváří a předem připravíte nasazení aktualizací softwaru v nástroji Configuration Manager a získat podrobný stav dokončených nasazení aktualizaci pomocí [řešení Update Management](automation-update-management.md). Pokud používáte Configuration Manager pro vytváření sestav o dodržování předpisů aktualizací, ale ne pro správu nasazení aktualizací se servery Windows, můžete pokračovat ve vytváření sestav pro Configuration Manager, zatímco se aktualizace zabezpečení spravují pomocí řešení Update Management.

## <a name="prerequisites"></a>Požadavky

* Musíte mít [řešení Update Management](automation-update-management.md) přidá do vašeho účtu Automation.
* Windows servery, které jsou aktuálně spravované prostředím Configuration Manager, musí také nahlásit pracovnímu prostoru Log Analytics, který má povolené řešení Update Management.
* Tato funkce je povolená v Configuration Manager aktuální větev verze 1606 a vyšší. Pokud chcete integrovat Configuration Manager lokalitu centrální správy nebo samostatnou primární lokalitu s protokoly Azure Monitor a importovanými kolekcemi, přečtěte si téma [připojení Configuration Manager k Azure monitor protokolů](../azure-monitor/platform/collect-sccm.md).  
* Pokud agenti Windows nedostávají aktualizace zabezpečení z Configuration Manageru, musí mít nakonfigurovanou komunikaci se serverem služeb Windows Server Update Services (WSUS) nebo musí mít přístup ke službě Microsoft Update.   

To, jak spravujete klienty hostované v Azure IaaS s využitím stávajícího prostředí Configuration Manageru, závisí primárně na připojení, které máte mezi datovými centry Azure a vaší infrastrukturou. Toto připojení ovlivňuje případné změny v návrhu, které je třeba provést v infrastruktuře Configuration Manageru, a náklady související s provedením těchto nezbytných změn. Pokud chcete lépe pochopit, jaké aspekty plánování byste měli vyhodnotit, než budete pokračovat dál, přečtěte si [nejčastější dotazy ke Configuration Manageru v Azure](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurace

### <a name="manage-software-updates-from-configuration-manager"></a>Správa aktualizací softwaru s využitím Configuration Manageru 

Pokud budete nadále spravovat nasazení aktualizace z Configuration Manageru, proveďte následující kroky. Azure Automation se připojí ke Configuration Manageru a aktualizací do klientských počítačů připojených k pracovnímu prostoru Log Analytics. Obsah aktualizace obsah je dostupný z mezipaměti klientského počítače, stejně jako v případě, kdy nasazení spravoval Configuration Manager.

1. Na webu nejvyšší úrovně v hierarchii Configuration Manager vytvořte nasazení aktualizace softwaru, a to pomocí postupu popsaného v [procesu nasazení softwarových aktualizací](/sccm/sum/deploy-use/deploy-software-updates). Jediným nastavením, které se musí nakonfigurovat jinak než u standardního nasazení, je možnost **Neinstalovat softwarové aktualizace**, která řídí řízení chování balíčku pro nasazení při stahování. Toto chování spravuje řešení Update Management vytvořením plánovaného nasazení aktualizace v dalším kroku.

1. Ve službě Azure Automation vyberte **Update Management**. Vytvořte nové nasazení kroků popsaných v [vytvoření nasazení aktualizace](automation-tutorial-update-management.md#schedule-an-update-deployment) a vyberte **importovat skupiny** na **typ** rozevíracího seznamu vyberte odpovídající Kolekce produktu Configuration Manager. Mějte na paměti následující důležité skutečnosti:. Pokud na vybrané kolekce zařízení Configuration Manageru je definované časové období údržby, členové této kolekce případném dalším sdílení dodržovat místo něj **doba trvání** nastavení definované ve plánované nasazení.
    b. Členové cílové kolekce musí mít připojení k Internetu (buď přímo, přes proxy server nebo přes bránu Log Analytics).

Po dokončení nasazení aktualizace prostřednictvím služby Azure Automation, bude cílových počítačů, které jsou členy vybrané skupiny počítačů z místní mezipaměti klienta instalovat aktualizace v naplánovaném čase. Můžete si [zobrazit stav nasazení aktualizací](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a monitorovat výsledky nasazení.

### <a name="manage-software-updates-from-azure-automation"></a>Správa aktualizací softwaru ze služby Azure Automation

Pokud chcete spravovat aktualizace pro virtuální počítače s Windows Serverem, které jsou klienty Configuration Manageru, musíte nakonfigurovat zásady klienta tak, aby funkce správy softwarových aktualizací byla zakázaná pro všechny klienty spravované tímto řešením. Nastavení klienta standardně cílí na všechna zařízení v hierarchii. Další informace o tomto nastavení zásad a jeho konfiguraci najdete v tématu věnovaném [postupu při konfigurace nastavení klienta v System Center Configuration Manageru](/sccm/core/clients/deploy/configure-client-settings).

Po provedení této změny konfigurace, vytvořte nové nasazení kroků popsaných v [vytvoření nasazení aktualizace](automation-tutorial-update-management.md#schedule-an-update-deployment) a vyberte **importovat skupiny** na **typ** rozevíracího seznamu vyberte příslušnou kolekci Configuration Manageru.

## <a name="next-steps"></a>Další kroky

