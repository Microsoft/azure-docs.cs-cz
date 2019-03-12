---
title: Selhání obnovení IaaS virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro zotavení po havárii pomocí služby Azure Site Recovery.
description: Zjistěte, jak převzít služby při obnovení virtuálních počítačů Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: 90ebea81c37c40dc5396d2e6378c7b9af52ad23d
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733107"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Selhání obnovení virtuálních počítačů Azure mezi oblastmi Azure

[Azure Site Recovery](site-recovery-overview.md) služby přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrátit služby po zpět z místních počítačů a Azure virtual machines (VM).

Tento kurz popisuje, jak navrátit služby po obnovení jednoho virtuálního počítače Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do primární oblasti, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Navrácení služeb po obnovení sekundárního virtuálního počítače
> * Znovunastavení ochrany primárního virtuálního počítače do sekundární oblasti

> [!NOTE]
>
> Tento kurz je určen pro uživatele provede kroky k převzetí služeb při selhání pro cílovou oblast a vraťte se minimální přizpůsobení; v případě, že chcete se dozvědět víc o různých aspektech, které jsou přidružené k převzetí služeb při selhání, včetně síťové požadavky na služby automation nebo řešení potíží, získáte v dokumentech v části "Jak na" pro virtuální počítače Azure.

## <a name="prerequisites"></a>Požadavky

> * Ujistěte se, že je virtuální počítač ve stavu převzetí služeb při selhání potvrzeno a zkontrolujte, zda je k dispozici primární oblasti a budete moci vytvářet a přistupovat k nové prostředky v ní.
> * Ujistěte se, že je tato opětovná ochrana povolená.

## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po opětovné chráněné virtuální počítače můžete předat zpět do primární oblasti jako a když chcete.

1. Přejděte do služby Recovery Services Vault. Klikněte na replikované položky a vyberte virtuální počítač, který se znovu zapnula ochrana.

2. Měli byste vidět následující. Všimněte si, že je podobný okna pro testovací převzetí služeb při selhání a převzetí služeb při selhání z primární oblasti.
![Navrácení služeb po obnovení primární](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Klikněte na testovací převzetí služeb při selhání provést testovací převzetí služeb zpět do primární oblasti. Zvolte bod obnovení a virtuální sítě pro testovací převzetí služeb a vyberte OK. Zobrazí se test, který virtuální počítač vytvořen v primární oblasti, které mohou získávat přístup a kontrolu.

4. Po testu převzetí služeb při selhání je vyhovující, můžete kliknout na vyčištění testovacího převzetí služeb při selhání vyčistit prostředky vytvořené v rámci zdrojové oblasti pro testovací převzetí služeb.

5. V replikované položky, vyberte virtuální počítač, který chcete převzetí služeb při selhání > převzetí služeb při selhání.

6. V převzetí služeb při selhání vyberte bod obnovení pro převzetí služeb při selhání. Můžete použít jednu z následujících možností:
    1. Nejnovější (výchozí): Tato možnost zpracuje všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO)
    2. Nejnovější zpracovaný: Tato možnost vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery
    3. Vlastní: Tuto možnost použijte k převzetí služeb při selhání konkrétnímu bodu obnovení. Tato možnost je užitečná při provádění testovací převzetí služeb při selhání

7. Vyberte příkaz Vypnout počítač před spuštěním převzetí služeb při selhání, pokud chcete, aby Site Recovery chcete pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Všimněte si, že Site Recovery nepodporuje vyčištění zdroj po převzetí služeb při selhání.

8. Sledujte průběh převzetí služeb při selhání na stránce úlohy

9. Po převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu připojíte. Pokud chcete vrátit jiný bod obnovení pro virtuální počítač, můžete změnit možnosti bodu obnovení.

10. Jakmile budete spokojeni s tím, neúspěšné přes virtuální počítač, můžete převzetí služeb při selhání potvrdit. Potvrzením dojde k odstranění všech bodů obnovení, které jsou ve službě k dispozici. Možnost změnit obnovení bodu již není k dispozici.

![Virtuální počítač v primární a sekundární oblasti](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Pokud vidíte na předchozím snímku obrazovky "ContosoWin2016" virtuálního počítače při selhání z USA (střed) – Východ USA a se nezdařilo obnovení z USA – Východ USA (střed).

Mějte prosím na paměti, že zotavení po Havárii virtuálních počítačů zůstanou ve stavu zrušení přidělení vypnutí. Toto chování je záměrné, protože Azure Site Recovery ukládá informace virtuálního počítače, které mohou být užitečné při převzetí služeb při selhání pro primární do sekundární oblasti později. Se vám neúčtují poplatky pro zrušení přidělení virtuálního počítače, abyste je měli uchovávat.

> [!NOTE]
> Zobrazit ["jak" část](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) další podrobnosti o pracovní postup opětovné ochrany a co se stane při opětovné ochrany.
