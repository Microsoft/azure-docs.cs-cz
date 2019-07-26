---
title: Konfigurace nastavení pro automatické vypnutí pro virtuální počítač v Azure DevTest Labs | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení automatického vypnutí pro virtuální počítač (VM), aby se virtuální počítač automaticky vypnul, když se nepoužívá.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361567"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Konfigurace nastavení automatické vypnutí pro virtuální počítač v Azure DevTest Labs
Azure DevTest Labs vám umožní řídit náklady a minimalizovat plýtvání v laboratoři tím, že spravují zásady (nastavení) pro každé testovací prostředí. V tomto článku se dozvíte, jak nakonfigurovat zásady pro automatické vypínání pro účet testovacího prostředí a nakonfigurovat nastavení pro automatické vypínání pro testovací prostředí v účtu testovacího prostředí. Pokud chcete zobrazit, jak nastavit všechny zásady testovacího prostředí, přečtěte si téma [Definování zásad testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Zásady pro automatické vypínání pro testovací prostředí
Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače v testovacím prostředí. Díky tomu můžete ušetřit náklady ze spuštěných počítačů, které se nepoužívají (nečinné). Zásady vypnutí můžete vystavit na všech virtuálních počítačích v testovacím prostředí centrálně, ale také ukládat uživatele testovacího prostředí s úsilím od nastavení plánu pro jednotlivé počítače. Tato funkce umožňuje nastavit zásady pro plán testovacího prostředí od nabídky bez řízení k úplnému řízení uživatelům testovacího prostředí. Jako vlastník testovacího prostředí můžete nakonfigurovat tuto zásadu provedením následujících kroků:

1. Na domovské stránce testovacího prostředí vyberte **Konfigurace a zásady**.
2. V levé nabídce vyberte v části **plány** možnost **zásady automatického vypnutí** .
3. Vyberte jednu z možností. Následující části obsahují další podrobnosti o těchto možnostech: Zásada set se vztahuje pouze na nové virtuální počítače vytvořené v testovacím prostředí, nikoli na již existující virtuální počítače. 

    ![Možnosti automatického vypnutí zásad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Konfigurace nastavení pro automatické vypnutí testovacího prostředí
Zásady pro automatické vypínání pomáhají minimalizovat odpadní laboratoře tím, že vám umožní určit čas, kdy se virtuální počítače testovacího prostředí vypnuly.

Chcete-li zobrazit (a změnit) zásady pro testovací prostředí, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí.   
4. Vyberte **Konfigurace a zásady**.

    ![Podokno nastavení zásad](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **automaticky** vypínání v části **plány**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Výběrem **zapnuto** tuto zásadu povolíte a  zakážete.
7. Pokud tuto zásadu povolíte, zadejte čas (a časové pásmo) pro vypnutí všech virtuálních počítačů v aktuálním testovacím prostředí.
8. Zadejte **hodnotu yes** nebo **No** pro možnost odeslání oznámení 15 minut před zadaným časem automatické vypnutí. Pokud zvolíte **Ano**, zadejte koncový bod adresy URL Webhooku nebo e-mailovou adresu, která určuje, kam chcete oznámení publikovat nebo odeslat. Uživatel obdrží oznámení a získá možnost zpoždění vypnutí. Další informace najdete v části [oznámení](#notifications) . 
9. Vyberte **Uložit**.

    Ve výchozím nastavení platí, že když je tato zásada povolená, vztahuje se na všechny virtuální počítače v aktuálním testovacím prostředí. Pokud chcete toto nastavení odebrat z konkrétního virtuálního počítače, otevřete podokno pro správu virtuálního počítače a změňte jeho nastavení pro automatické **vypnutí** .

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Konfigurace nastavení pro automatické vypnutí pro virtuální počítač

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plán a může se odhlásit.
Pokud je tato možnost zásad nastavená pro testovací prostředí, uživatelé můžou tento plán testovacího prostředí přepsat nebo se z něj odhlásit. Tato možnost uděluje uživatelům testovacího prostředí plnou kontrolu nad plánem automatického vypnutí svých virtuálních počítačů. Uživatelé testovacího prostředí uvidí na stránce plán automatického vypínání virtuálních počítačů žádné změny.

![Možnost automatického vypnutí zásady – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nemůže se odhlásit.
Pokud je tato možnost zásad nastavená pro testovací prostředí, můžou uživatelé potlačit plán testovacího prostředí. Nemůžou ale odhlásit zásady automatického vypnutí. Tato možnost zajistí, že každý počítač v testovacím prostředí je v plánu automatického vypnutí. Uživatelé testovacího prostředí můžou aktualizovat plán automatického vypnutí svých virtuálních počítačů a nastavit oznámení o vypnutí.

![Možnost automatického vypnutí zásady – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánem nastaveným správcem testovacího prostředí.
Pokud je tato možnost zásad nastavená pro testovací prostředí, uživatelé nemůžou potlačit nebo odhlásit plán testovacího prostředí. Tato možnost nabízí správci testovacího prostředí úplnou kontrolu nad plánem pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžou pro svoje virtuální počítače nastavit jenom oznámení o automatickém vypnutí.

![Možnost automatického vypnutí zásady – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Oznámení
Jakmile se automatické vypnutí nastaví vlastníkem testovacího prostředí, pošle se oznámení na uživatele testovacího prostředí 15 minut, než se automatické vypnutí aktivuje, pokud bude ovlivněn některý z jeho virtuálních počítačů. Tato možnost dává uživatelům testovacího prostředí možnost uložit práci před vypnutím. Oznámení také obsahuje odkazy na jednotlivé virtuální počítače pro následující akce:

- Přeskočit automatické vypínání pro tentokrát
- Automatické vypnutí se odhlásí po hodinu nebo 2 hodinách, aby mohli na virtuálním počítači dál pracovat.

Oznámení se odesílá prostřednictvím nakonfigurovaného koncového bodu webového zavěšení nebo e-mailové adresy zadané vlastníky testovacího prostředí v nastavení automatické vypnutí. Webhooky umožňují vytvářet nebo nastavovat integrace, které se přihlásí k odběru určitých událostí. Když se aktivuje jedna z těchto událostí, DevTest Labs pošle datovou část HTTP POST do konfigurované adresy URL Webhooku. Další informace o webhookech najdete v tématu [Vytvoření Webhooku nebo rozhraní API Azure Function](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Doporučujeme používat Webhooky, protože jsou široce podporované různými aplikacemi (například časová rezerva, Azure Logic Apps a tak dále) a umožňují vám implementovat vlastní způsob odesílání oznámení. Příklad přijetí oznámení o automatickém vypnutí z e-mailů pomocí Azure Logic Apps najdete v tématu[Vytvoření aplikace logiky, která přijímá e-mailová oznámení](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>Další postup
Informace najdete [v tématu Správa zásad pro automatické vypínání v testovacím prostředí v Azure DevTest Labs](devtest-lab-auto-shutdown.md)
