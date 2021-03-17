---
title: Postup reakce na Správce prostředků výstrahy na Azure Defender
description: Přečtěte si o krocích nezbytných pro reakci na výstrahy z Azure Defenderu pro Správce prostředků
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754652"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Reakce na Správce prostředků upozornění na Azure Defender

Když obdržíte výstrahu od Azure Defenderu pro Správce prostředků, doporučujeme, abyste si vyšetřili a odpověděli na výstrahu, jak je popsáno níže. Azure Defender pro Správce prostředků chrání všechny připojené prostředky, takže i když jste obeznámeni s aplikací nebo uživatelem, který výstrahu aktivoval, je důležité ověřit situaci okolní s každou výstrahou.  


## <a name="step-1-contact"></a>Krok 1. Kontakt

1. Obraťte se na vlastníka prostředku a zjistěte, zda bylo chování očekáváno nebo záměrné.
1. Pokud je aktivita očekávaná, zavřete výstrahu.
1. Pokud je aktivita neočekávaná, považovat související uživatelské účty, odběry a virtuální počítače za napadené a zmírnit je, jak je popsáno v následujícím kroku.

## <a name="step-2-immediate-mitigation"></a>Krok 2. Okamžité zmírnění 

1. Napravení ohrožených uživatelských účtů:
    - Pokud nejsou známy, odstraňte je, protože byly vytvořeny pomocí objektu actor pro hrozby.
    - Pokud jsou známé, změňte přihlašovací údaje pro ověřování.
    - Pomocí protokolů aktivit Azure zkontrolujte všechny aktivity provedené uživatelem a zjistěte, které z nich jsou podezřelé.

1. Opravit napadené odběry:
    - Odebrat všechny neznámé Runbooky z ohroženého účtu Automation
    - Zkontrolujte oprávnění IAM pro předplatné a odeberte oprávnění pro jakýkoli neznámý uživatelský účet.
    - Zkontrolujte všechny prostředky Azure v rámci předplatného a odstraňte všechny, které nejsou známé.
    - Zkontrolujte a prozkoumejte všechny výstrahy zabezpečení pro předplatné ve službě Azure Security Center
    - Protokoly aktivit Azure slouží ke kontrole všech aktivit prováděných v rámci předplatného a zjištění těch, které jsou podezřelé.

1. Náprava napadených virtuálních počítačů
    - Změna hesel pro všechny uživatele
    - Spuštění úplné kontroly malwaru v počítači
    - Obnovení počítačů ze zdroje bez malwaru z Image


## <a name="next-steps"></a>Další kroky

Tato stránka vysvětluje proces reakce na výstrahu z Azure Defenderu pro Správce prostředků. Související informace najdete na následujících stránkách:

- [Seznámení s Azure Defenderem pro Správce prostředků](defender-for-resource-manager-introduction.md)
- [Potlačit výstrahy z Azure Defenderu](alerts-suppression-rules.md)
- [Průběžný export Security Center dat](continuous-export.md)