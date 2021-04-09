---
title: Rychlý Start – nasazení a správa protokolů toku NSG pomocí Azure Policy
titleSuffix: Azure Network Watcher
description: Tento článek vysvětluje, jak pomocí integrovaných zásad spravovat nasazení protokolů toku NSG.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011097"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Rychlý Start: nasazení a správa protokolů toku NSG pomocí Azure Policy 

## <a name="overview"></a>Přehled
Služba Azure Policy pomáhá vynutit standardy organizace a vyhodnotit dodržování předpisů s podporou škálování. Běžné případy použití pro Azure Policy zahrnují implementaci zásad správného řízení pro konzistenci prostředků, dodržování legislativních předpisů, zabezpečení, náklady a správu. V tomto článku použijeme dvě předdefinované zásady, které jsou dostupné pro protokoly toku NSG ke správě nastavení protokolů toku. První zásada označí všechny skupin zabezpečení sítě bez povolených protokolů Flow. Druhá zásada automaticky nasadí protokoly toku pro skupin zabezpečení sítě bez povolených protokolů Flow. 

Pokud vytváříte zásady Azure poprvé, můžete si přečíst: 
- [Přehled služby Azure Policy](../governance/policy/overview.md) 
- [Kurz pro vytváření zásad](../governance/policy/assign-policy-portal.md#create-a-policy-assignment)


## <a name="locate-the-policies"></a>Vyhledat zásady
1. Přejít na Azure Portal – [Portal.Azure.com](https://portal.azure.com) 

Přejít na stránku Azure Policy hledáním zásad v horní ![ domovské stránce zásad vyhledávacího panelu](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Přejděte na kartu **přiřazení** v levém podokně.

![Karta přiřazení](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Klikněte na tlačítko **přiřadit zásadu** . 

![Tlačítko přiřadit zásadu](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Kliknutím na tři tečky v části definice zásad zobrazíte dostupné zásady.

5. Použijte filtr typu a klikněte na předdefinované. Pak vyhledejte "protokol toku"

Měly by se zobrazit dvě vestavěné zásady pro seznam zásad protokolů toků. ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Vyberte zásadu, kterou chcete přiřadit.

- *"Protokol toku by měl být nakonfigurovaný pro každou skupinu zabezpečení sítě"* je zásada auditu, která označuje nekompatibilní skupin zabezpečení sítě, která je skupin zabezpečení sítě bez povoleného protokolování toku.
- *"Nasazení prostředku protokolu toku s cílovou skupinou zabezpečení sítě"* je zásada s akcí nasazení, umožňuje protokolovat protokoly všech skupin zabezpečení sítě bez protokolů toků.

Pro každou níže uvedenou zásadu jsou k dispozici samostatné pokyny.  

## <a name="audit-policy"></a>Zásady auditu 

### <a name="how-the-policy-works"></a>Jak zásady fungují

Zásada kontroluje všechny existující objekty ARM typu "Microsoft. Network/networkSecurityGroups", což znamená, že se ve všech skupin zabezpečení sítě v daném oboru vyhledává a kontroluje existenci propojených protokolů toku prostřednictvím vlastnosti log Flow NSG. Pokud vlastnost neexistuje, NSG je označena příznakem.

Pokud chcete zobrazit úplnou definici zásad, můžete navštívit [kartu definice](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) a vyhledat zásadu "protokoly toků".

### <a name="assignment"></a>Přiřazení

1. Vyplňte podrobnosti zásady.

- Rozsah: je to společná volba předplatného, můžete také zvolit skupinu pro správu nebo skupinu prostředků, které jsou pro vás relevantní.  
- Definice zásad: měla by být zvolena tak, jak je uvedeno v části "vyhledání zásad".
- Přiřazení: vyberte popisný název. 

2. Pokud chcete zkontrolovat přiřazení, klikněte na zkontrolovat + vytvořit.

Zásady nevyžadují žádné parametry. Když přiřazujete zásady auditu, nemusíte tyto údaje vyplnit na kartě "náprava".  

![Kontrola zásad auditu](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Výsledky

Chcete-li kontrolovat výsledky, otevřete kartu dodržování předpisů a vyhledejte název svého přiřazení.
Po spuštění zásady byste měli vidět podobné jako na následujícím snímku obrazovky. V případě, že se vaše zásada nespustí, počkejte nějakou dobu. 

![Výsledky zásad auditu](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Zásada nasazení-if-neexistující 

### <a name="policy-structure"></a>Struktura zásad

Zásada kontroluje všechny existující objekty ARM typu "Microsoft. Network/networkSecurityGroups", což znamená, že se ve všech skupin zabezpečení sítě v daném oboru vyhledává a kontroluje existenci propojených protokolů toku prostřednictvím vlastnosti log Flow NSG. Pokud vlastnost neexistuje, tato zásada nasadí protokol toku. 

Pokud chcete zobrazit úplnou definici zásad, můžete navštívit [kartu definice](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) a vyhledat zásadu "protokoly služby Flow". 

### <a name="assignment"></a>Přiřazení

1. Vyplňte podrobnosti zásady.

- Rozsah: je to společná volba předplatného, můžete také zvolit skupinu pro správu nebo skupinu prostředků, které jsou pro vás relevantní.  
- Definice zásad: měla by být zvolena tak, jak je uvedeno v části "vyhledání zásad".
- Přiřazení: vyberte popisný název. 

2. Přidat parametry zásad 

Služba Network Watcher je místní služba. Tyto parametry umožňují spustit akci zásad nasazování protokolů toku. 
- Oblast NSG: oblasti Azure, na kterých je zásada cílená
- ID úložiště: úplné ID prostředku účtu úložiště. Poznámka: Tento účet úložiště by měl být ve stejné oblasti jako NSG. 
- Sledovací procesy sítě RG: název skupiny prostředků, která obsahuje prostředek Network Watcher. Pokud jste ho přejmenovali, můžete zadat "NetworkWatcherRG", což je výchozí nastavení.
- Network Watcher název: název služby sledovacího procesu regionální sítě. Formát: NetworkWatcher_RegionName. Příklad: NetworkWatcher_centralus. Prohlédněte si úplný seznam.

![Parametry zásad Rewards](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Přidat podrobnosti o nápravě

- Pokud chcete, aby zásady ovlivnily stávající prostředky, zaškrtněte možnost u možnosti vytvořit úlohu nápravy. 
- Možnost vytvořit spravovanou identitu by už měla být zaškrtnutá.
- Pro spravovanou identitu se vybere stejné umístění jako předchozí. 
- K použití této zásady budete potřebovat oprávnění Přispěvatel nebo Owner. Pokud máte tato oprávnění, neměli byste zobrazovat žádné chyby.

![Náprava zásad Rewards](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Pokud chcete zkontrolovat přiřazení, klikněte na zkontrolovat + vytvořit. na následujícím snímku obrazovky by se měla zobrazit podobný text.

![Kontrola zásad Rewards](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Výsledky

Chcete-li kontrolovat výsledky, otevřete kartu dodržování předpisů a vyhledejte název svého přiřazení.
Po vaší zásadě byste měli vidět, jako je třeba následující snímek obrazovky. V případě, že se vaše zásada nespustí, počkejte nějakou dobu.

![Výsledky zásad Rewards](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Další kroky 

-   V tomto [kurzu](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) získáte hlubší použití šablon ARM k nasazení protokolů toku a analýza provozu.
-   Další informace o [Network Watcher](./index.yml)