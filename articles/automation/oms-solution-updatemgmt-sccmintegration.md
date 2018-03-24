---
title: Cíl aktualizace pomocí SCCM kolekcí ve službě Azure Automation - Správa aktualizací
description: Tento článek vám objasní, jak pomocí tohoto řešení nakonfigurovat System Center Configuration Manager a spravovat aktualizace počítačů spravovaných pomocí SCCM.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 481195538a8c0ece572b4589ea2c2303e559fc44
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Správa aktualizací integrovat nástroje System Center Configuration Manager

Zákazníci, kteří investovali do System Center Configuration Manageru pro správu počítačů, serverů a mobilních zařízení využívají jeho odolnost a další přednosti také při správě aktualizací softwaru jako součást cyklu správy softwarových aktualizací (SUM).

Sestavy a aktualizujte spravované Windows servery tak, že vytváření a předem pracovní nasazení aktualizací softwaru v nástroji Configuration Manager a získat podrobné informace o stavu nasazení dokončené aktualizací pomocí [řešení pro správu aktualizovat](automation-update-management.md). Pokud používáte nástroj Configuration Manager pro vytváření sestav dodržování předpisů aktualizací, ale není pro správu nasazení aktualizací s Windows serverech, můžete pokračovat, vytváření sestav nástroje Configuration Manager při aktualizace zabezpečení se spravují prostřednictvím řešení pro správu aktualizovat.

## <a name="prerequisites"></a>Požadavky

* Musíte mít [řešení pro správu aktualizací](automation-update-management.md) přidána k vašemu účtu Automation.
* Servery Windows aktuálně spravované pomocí prostředí System Center Configuration Manageru také musí do pracovního prostoru Log Analytics nahlásit, že mají povolené řešení Update Management.
* Tato funkce je povolena v System Center Configuration Manager aktuální větve verze 1606 a vyšší. Pokud chcete integrovat samostatný primární web nebo centrální web správy Configuration Manageru s Log Analytics, přečtěte si téma věnované [propojení Configuration Manageru k Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Pokud agenti Windows nedostávají aktualizace zabezpečení z Configuration Manageru, musí mít nakonfigurovanou komunikaci se serverem služeb Windows Server Update Services (WSUS) nebo musí mít přístup ke službě Microsoft Update.   

To, jak spravujete klienty hostované v Azure IaaS s využitím stávajícího prostředí Configuration Manageru, závisí primárně na připojení, které máte mezi datovými centry Azure a vaší infrastrukturou. Toto připojení ovlivňuje případné změny v návrhu, které je třeba provést v infrastruktuře Configuration Manageru, a náklady související s provedením těchto nezbytných změn. Pokud chcete lépe pochopit, jaké aspekty plánování byste měli vyhodnotit, než budete pokračovat dál, přečtěte si [nejčastější dotazy ke Configuration Manageru v Azure](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurace

### <a name="manage-software-updates-from-configuration-manager"></a>Správa aktualizací softwaru s využitím Configuration Manageru 

Pokud budete nadále spravovat nasazení aktualizace z Configuration Manageru, proveďte následující kroky. Služby Azure Automation se připojuje ke Configuration Manageru a použití aktualizací na klientské počítače připojené k pracovní prostor analýzy protokolů. Obsah aktualizace obsah je dostupný z mezipaměti klientského počítače, stejně jako v případě, kdy nasazení spravoval Configuration Manager.

1. Na webu nejvyšší úrovně v hierarchii Configuration Manager vytvořte nasazení aktualizace softwaru, a to pomocí postupu popsaného v [procesu nasazení softwarových aktualizací](/sccm/sum/deploy-use/deploy-software-updates). Jediným nastavením, které se musí nakonfigurovat jinak než u standardního nasazení, je možnost **Neinstalovat softwarové aktualizace**, která řídí řízení chování balíčku pro nasazení při stahování. Toto chování je spravovaný řešení pro správu aktualizovat tak, že vytvoříte nasazení naplánované aktualizace v dalším kroku.

1. Ve službě Azure Automation, vyberte **správy aktualizací**. Vytvořte nové nasazení kroků popsaných v [vytváření nasazení aktualizací](automation-tutorial-update-management.md#schedule-an-update-deployment) a vyberte **importovat skupiny** na **typ** rozevíracího seznamu vyberte odpovídající Kolekce produktu Configuration Manager. Mějte na paměti následující důležité skutečnosti:. Pokud ve vybrané kolekci zařízení nástroje Configuration Manager je definována okna údržby, členy kolekce respektovat ho místo **doba trvání** nastavení definované v naplánované nasazení.
    b. Členové cílové kolekce musí mít připojení k internetu (přímo, přes proxy server nebo přes bránu OMS).

Po dokončení nasazení aktualizace pomocí Azure Automation, cílových počítačů, které jsou členy skupiny vybraného počítače nainstaluje aktualizace v naplánovaném čase ze své místní mezipaměti klienta. Můžete si [zobrazit stav nasazení aktualizací](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a monitorovat výsledky nasazení.

### <a name="manage-software-updates-from-azure-automation"></a>Správa aktualizací softwaru z Azure Automation.

Pokud chcete spravovat aktualizace pro virtuální počítače s Windows Serverem, které jsou klienty Configuration Manageru, musíte nakonfigurovat zásady klienta tak, aby funkce správy softwarových aktualizací byla zakázaná pro všechny klienty spravované tímto řešením. Nastavení klienta standardně cílí na všechna zařízení v hierarchii. Další informace o tomto nastavení zásad a jeho konfiguraci najdete v tématu věnovaném [postupu při konfigurace nastavení klienta v System Center Configuration Manageru](/sccm/core/clients/deploy/configure-client-settings).

Po provedení této změny konfigurace, můžete vytvořit nové nasazení kroků popsaných v [vytváření nasazení aktualizací](automation-tutorial-update-management.md#schedule-an-update-deployment) a vyberte **importovat skupiny** na **typ** rozevíracího seznamu vyberte odpovídající kolekci nástroje Configuration Manager.

## <a name="next-steps"></a>Další postup
