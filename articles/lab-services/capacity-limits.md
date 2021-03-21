---
title: Omezení kapacity v Azure Lab Services
description: Přečtěte si o limitech kapacity (omezeních virtuálních počítačů) v Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9866628cd11ec8df67e6fe16ae8806f0f30ae9a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491014"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Omezení kapacity v Azure Lab Services
Azure Lab Services má v předplatných Azure výchozí omezení kapacity, které bude vyhovovat omezením kvóty Azure COMPUTE a zmírnit podvod. Všechna předplatná Azure budou mít počáteční limit kapacity, který se může lišit v závislosti na typu předplatného, počtu standardních výpočetních jader a jádrech GPU dostupných v Azure Lab Services. Omezuje počet virtuálních počítačů, které můžete v laboratoři vytvořit, než budete muset požádat o zvýšení limitu.  

Pokud se blížíte nebo jste dosáhli limitu jader virtuálních počítačů vašeho předplatného, zobrazí se při pokusu o provedení akcí, které vytvářejí další virtuální počítače, zprávy z Azure Lab Services. Například: 

- Vytvoření testovacího prostředí
- Publikování testovacího prostředí
- Úprava kapacity testovacího prostředí pro přidání dalších virtuálních počítačů do stávajícího testovacího prostředí

Tyto akce můžou být zakázané i v případě, že jste už dosáhli limitu jader. 

![Základní omezení – varovná zpráva](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Předplatná s výchozím limitem nulových jader
Některé vzácné typy předplatného, které jsou častěji používané k podvodům, můžou mít výchozí limit 0 standardních jader a 0 jader GPU. Pokud používáte některý z těchto typů předplatného, správce vytvářející účet testovacího prostředí bude muset požádat o zvýšení limitu, než budete moct Azure Lab Services použít. 

Správce může pomocí těchto kroků požádat o zvýšení limitu:  

1.  V rámci vašeho předplatného [vytvořte účet testovacího prostředí](tutorial-setup-lab-account.md).
2.  Na stránce **Přehled** účtu testovacího prostředí klikněte v horní části na tlačítko **Zvětšit limit žádosti** . 
3.  Pomocí kroků ve formuláři odešlete žádost o podporu pro zvýšení limitu.

## <a name="request-a-limit-increase"></a>Požádat o zvýšení limitu
Pokud dosáhnete limitu jader, můžete požádat o zvýšení limitu, aby bylo možné dál používat Azure Lab Services. Proces žádosti je kontrolní bod, aby se zajistilo, že vaše předplatné není v žádném případě podvodu nebo neúmyslného a náhlého nasazení s větším rozsahem.

Zprávy o limitu jader virtuálních počítačů na portálu Azure Lab Services zahrnují odkaz pro vyžádání zvýšení limitu. Odkaz otevře novou kartu prohlížeče, kde můžete vytvořit novou žádost o podporu. Informace o typu problému, předplatném a typu kvóty se automaticky vyplní, jak je znázorněno na následujícím obrázku: 

![Nová žádost o podporu](./media/capacity-limits/new-support-request.png)


Pak budete vyzváni k zadání dalších informací o zvýšení limitu. Do pole **Popis** zadejte následující podrobnosti:

- Co se pokoušíte (například vytvoření testovacího prostředí pro výuku počítačové vědecké třídy, spuštění Hackathon atd.)
- Velikost virtuálního počítače, kterou používáte pro toto testovací prostředí
- Počet virtuálních počítačů, které potřebujete

Jakmile odešlete žádost o podporu, zkontrolujeme žádost. V případě potřeby vás budeme kontaktovat, abychom vám pomohli získat další podrobnosti. 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek:
- [Příručka pro správce – Změna velikosti virtuálního počítače](administrator-guide.md#vm-sizing)
- [Nejčastější dotazy](classroom-labs-faq.md)