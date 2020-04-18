---
title: Použití azure správy aktualizací s klienty Configuration Manageru
description: Tento článek vám pomůže nakonfigurovat nástroj Microsoft Endpoint Configuration Manager s tímto řešením pro nasazení aktualizací softwaru pro klienty ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618721"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Nasazení aktualizací klientům nástroje Microsoft Endpoint Configuration Manager pomocí správy aktualizací

Zákazníci, kteří investovali do nástroje Microsoft Endpoint Configuration Manager pro správu počítačů, serverů a mobilních zařízení, se také spoléhají na svou sílu a vyspělost při správě aktualizací softwaru v rámci cyklu správy aktualizací softwaru (SUM).

Spravované servery Windows můžete vykazovat a aktualizovat vytvořením a předstagingním nasazením aktualizací softwaru ve Správci konfigurace a získat podrobný stav dokončených nasazení aktualizací pomocí [řešení správy aktualizací](automation-update-management.md). Pokud nástroj Configuration Manager používáte pro vytváření sestav dodržování předpisů aktualizací, ale ne pro správu nasazení aktualizací se servery windows, můžete pokračovat v podávání zpráv nástroji Configuration Manager, zatímco aktualizace zabezpečení jsou spravovány pomocí řešení správy aktualizací.

## <a name="prerequisites"></a>Požadavky

* Do účtu Automation musíte mít [přidáno řešení správy aktualizací.](automation-update-management.md)
* Servery se systémem Windows, které jsou aktuálně spravovány prostředím nástroje Configuration Manager, musí také hlásit do pracovního prostoru Log Analytics, který má také povolené řešení správy aktualizací.
* Tato funkce je povolena v nástroji Configuration Manager v aktuální větvi verze 1606 a vyšší. Pokud chcete integrovat lokalitu centrální správy nástroje Configuration Manager nebo samostatnou primární lokalitu s protokoly Azure Monitor a kolekcemi importů, přečtěte si [stránku Connect Configuration Manager s protokoly Azure Monitoru](../azure-monitor/platform/collect-sccm.md).  
* Pokud agenti Windows nedostávají aktualizace zabezpečení z Configuration Manageru, musí mít nakonfigurovanou komunikaci se serverem služeb Windows Server Update Services (WSUS) nebo musí mít přístup ke službě Microsoft Update.   

To, jak spravujete klienty hostované v Azure IaaS s využitím stávajícího prostředí Configuration Manageru, závisí primárně na připojení, které máte mezi datovými centry Azure a vaší infrastrukturou. Toto připojení ovlivňuje případné změny v návrhu, které je třeba provést v infrastruktuře Configuration Manageru, a náklady související s provedením těchto nezbytných změn. Pokud chcete lépe pochopit, jaké aspekty plánování byste měli vyhodnotit, než budete pokračovat dál, přečtěte si [nejčastější dotazy ke Configuration Manageru v Azure](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurace

### <a name="manage-software-updates-from-configuration-manager"></a>Správa aktualizací softwaru s využitím Configuration Manageru 

Pokud budete nadále spravovat nasazení aktualizace z Configuration Manageru, proveďte následující kroky. Azure Automation se připojí ke službě Configuration Manager a použije aktualizace klientských počítačů připojených k pracovnímu prostoru Analýzy protokolů. Obsah aktualizace obsah je dostupný z mezipaměti klientského počítače, stejně jako v případě, kdy nasazení spravoval Configuration Manager.

1. Vytvořte nasazení aktualizací softwaru z lokality nejvyšší úrovně v hierarchii nástroje Configuration Manager pomocí postupu popsaného v části [Nasazení aktualizací softwaru](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Jediným nastavením, které se musí nakonfigurovat jinak než u standardního nasazení, je možnost **Neinstalovat softwarové aktualizace**, která řídí řízení chování balíčku pro nasazení při stahování. Toto chování je spravováno řešením správy aktualizací vytvořením naplánovaného nasazení aktualizace v dalším kroku.

1. V Azure Automation vyberte **Správa aktualizací**. Vytvořte nové nasazení podle kroků popsaných v [části Vytvoření nasazení aktualizace](automation-tutorial-update-management.md#schedule-an-update-deployment) a v rozevíracím seznamu **Typ** vyberte **příslušnou** kolekci nástroje Configuration Manager. Mějte na paměti následující důležité body: a. Pokud je okno údržby definováno ve vybrané kolekci zařízení Nástroje configuration manageru, členové kolekce ho respektují namísto nastavení **Doba trvání** definovanév naplánovaném nasazení.
    b. Členové cílové kolekce musí mít připojení k Internetu (přímé, prostřednictvím proxy serveru nebo prostřednictvím brány Log Analytics).

Po dokončení nasazení aktualizace prostřednictvím Azure Automation budou cílové počítače, které jsou členy vybrané skupiny počítačů, instalovat aktualizace v naplánovaný čas z mezipaměti místního klienta. Můžete si [zobrazit stav nasazení aktualizací](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a monitorovat výsledky nasazení.

### <a name="manage-software-updates-from-azure-automation"></a>Správa aktualizací softwaru z Azure Automation

Pokud chcete spravovat aktualizace pro virtuální počítače s Windows Serverem, které jsou klienty Configuration Manageru, musíte nakonfigurovat zásady klienta tak, aby funkce správy softwarových aktualizací byla zakázaná pro všechny klienty spravované tímto řešením. Nastavení klienta standardně cílí na všechna zařízení v hierarchii. Další informace o tomto nastavení zásad a jeho konfiguraci naleznete v části [Konfigurace nastavení klienta .](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)

Po provedení této změny konfigurace vytvoříte nové nasazení podle kroků popsaných v [části Vytvoření nasazení aktualizace](automation-tutorial-update-management.md#schedule-an-update-deployment) a v rozevíracím seznamu **Typ** vyberte příslušnou kolekci Nástroje pro konfiguraci. **Imported groups**

## <a name="next-steps"></a>Další kroky

