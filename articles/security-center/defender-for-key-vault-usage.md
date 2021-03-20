---
title: Reakce na upozornění Azure Defenderu pro Key Vault
description: Přečtěte si o krocích nezbytných pro reakci na výstrahy z Azure Defenderu pro Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96122201"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Reakce na upozornění Azure Defenderu pro Key Vault
Když obdržíte výstrahu od Azure Defenderu pro Key Vault, doporučujeme, abyste si vyšetřili a odpověděli na výstrahu, jak je popsáno níže. Azure Defender pro službu Key Vault chrání aplikace a přihlašovací údaje, takže i v případě, že znáte aplikaci nebo uživatele, který upozornění aktivoval, je potřeba u každého upozornění ověřit konkrétní situaci.  

Každé upozornění z Azure Defenderu pro Key Vault zahrnuje tyto prvky:

- ID objektu
- Hlavní název uživatele nebo IP adresa podezřelého prostředku

> [!TIP]
> Na základě *typu* přístupu, ke kterému došlo, nemusí být některá pole k dispozici. Například pokud k trezoru klíčů přistupovala aplikace, nezobrazí se přidružený hlavní název uživatele (UPN). Pokud provoz nepocházel z Azure, nezobrazí se ID objektu.

## <a name="step-1-contact"></a>Krok 1. Kontakt

1. Ověřte, jestli provoz pochází z vašeho tenanta Azure. Pokud je povolená brána firewall trezoru klíčů, je možné, že jste poskytli přístup k uživateli nebo aplikaci, která aktivovala tuto výstrahu.
1. Pokud nemůžete ověřit zdroj provozu, pokračujte [krokem 2. Okamžité zmírnění](#step-2-immediate-mitigation).
1. Pokud můžete identifikovat zdroj provozu ve vašem tenantovi, obraťte se na uživatele nebo vlastníka aplikace. 

> [!CAUTION]
> Azure Defender pro Key Vault je navržený tak, aby pomohla identifikovat podezřelou aktivitu způsobenou odcizenými přihlašovacími údaji. Upozornění **nenechávejte** jednoduše, protože znáte uživatele nebo aplikaci. Obraťte se na vlastníka aplikace nebo uživatele a ověřte, že aktivita byla legitimní. V případě potřeby můžete vytvořit pravidlo potlačení, které odstraní šum. Další informace najdete v [potlačit výstrahy z Azure Defenderu](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>Krok 2. Okamžité zmírnění 
Pokud uživatele nebo aplikaci nepoznáváte nebo pokud se domníváte, že přístup by neměl být autorizovaný:

- Pokud přenos pochází z nerozpoznané IP adresy:
    1. Povolte bránu Azure Key Vault firewall, jak je popsáno v tématu [konfigurace Azure Key Vault firewallů a virtuálních sítí](../key-vault/general/network-security.md).
    1. Nakonfigurujte bránu firewall pomocí důvěryhodných prostředků a virtuálních sítí.

- Pokud byl zdrojem výstrahy neoprávněná aplikace nebo podezřelý uživatel:
    1. Otevřete nastavení zásad přístupu trezoru klíčů.
    1. Odeberte odpovídající objekt zabezpečení nebo omezte operace, které může objekt zabezpečení provést.  

- Pokud má zdroj výstrahy Azure Active Directory roli v tenantovi:
    1. Obraťte se na správce.
    1. Určete, jestli je potřeba snížit nebo odvolat Azure Active Directory oprávnění.

## <a name="step-3-identify-impact"></a>Krok 3. Identifikovat dopad 
Pokud je dopad zmírněníný, prozkoumejte tajné klíče v trezoru klíčů, které byly ovlivněny:
1. Otevřete na svém Azure Key Vault stránku zabezpečení a podívejte se na aktivované upozornění.
1. Vyberte konkrétní výstrahu, která se aktivovala.
    Projděte si seznam tajných kódů, ke kterým bylo přistup, a časové razítko.
1. Případně, pokud máte povolené diagnostické protokoly trezoru klíčů, přečtěte si předchozí operace pro odpovídající IP adresu volajícího, objekt zabezpečení uživatele nebo ID objektu.  

## <a name="step-4-take-action"></a>Krok 4: Provést akci 
Po zkompilování seznamu tajných kódů, klíčů a certifikátů, k nimž získal podezřelý uživatel nebo aplikace, je vhodné tyto objekty okamžitě otočit.

1. Ovlivněné tajné klíče by se měly zakázat nebo odstranit z trezoru klíčů.
1. Pokud byly pověření použity pro konkrétní aplikaci:
    1. Obraťte se na správce aplikace a požádejte je o audit svého prostředí pro jakékoli použití ohrožených přihlašovacích údajů, protože byly ohroženy.
    1. Pokud se používaly ohrožené přihlašovací údaje, vlastník aplikace by měl identifikovat informace, ke kterým byl přidaný, a zmírnit dopad.


## <a name="next-steps"></a>Další kroky

Tato stránka vysvětluje proces reakce na výstrahu z Azure Defenderu pro Key Vault. Související informace najdete na následujících stránkách:

- [Seznámení s Azure Defenderem pro Key Vault](defender-for-key-vault-introduction.md)
- [Potlačit výstrahy z Azure Defenderu](alerts-suppression-rules.md)
- [Průběžný export Security Center dat](continuous-export.md)