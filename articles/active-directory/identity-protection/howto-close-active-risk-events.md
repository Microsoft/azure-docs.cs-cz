---
title: Postup zavření active rizikových událostí ve službě Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Další informace o možnostech, že abyste měli zavřít aktivní rizikové události.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50448cc5d4fe6714aa0cd29216209506eccf7a7c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201614"
---
# <a name="how-to-close-active-risk-events"></a>Jak: Zavření aktivních rizikových událostí

S [rizikových událostí](../reports-monitoring/concept-risk-events.md), Azure Active Directory zjistí ukazatele pro potenciálně ohrožení uživatelských účtů. Jako správce budete chtít získat všechny rizikové události jsou zavřené, tak, aby ovlivnění uživatelé se už riziku.

Tento článek obsahuje přehled dalších možností, budete muset zavřít aktivní rizikové události.

## <a name="options-to-close-risk-events"></a>Možnosti pro zavření rizikových událostí 

Stav rizikové události je buď **aktivní** nebo **uzavřeno**. Všechny aktivní rizikové události přispívat k výpočtu hodnoty nazvané úroveň rizika uživatele. Úroveň rizika uživatele je indikátorem (nízká, střední, vysoká) pravděpodobnost, že není účet v bezpečí. 

Zavřít aktivní rizikových událostí, máte následující možnosti:

- Vyžadovat resetování hesla se zásady rizik uživatelů

- Resetování hesla ruční
 
- Skrýt všechny rizikové události 

- Ručně ukončete jednotlivé rizikových událostí



## <a name="require-password-reset-with-a-user-risk-policy"></a>Vyžadovat resetování hesla se zásady rizik uživatelů

Pokud nakonfigurujete [zásady podmíněného přístupu na základě rizika uživatele](howto-user-risk-policy.md), můžete vyžadovat změnu hesla, pokud se automaticky zjistí určitá úroveň rizika uživatele. 

![Resetování hesla](./media/howto-close-active-risk-events/13.png)

Resetování hesla ukončí všechny aktivní rizika události související uživatele a přináší identitu zpátky do bezpečného stavu. Použití zásady rizik uživatelů je upřednostňovanou metodou zavřete aktivní rizikové události, protože tato metoda je automatické. Neexistuje žádná interakce potřebné mezi ovlivněné uživatele a pomoc HelpDesk nebo správce.

Ale pomocí zásady rizik uživatelů není vždy použít. To se týká, například:

- Uživatelé, kteří se nezaregistrovali pro ověřování službou Multi-Factor Authentication (MFA).
- Uživatelé s aktivní rizikové události, které byly odstraněny.
- Šetření, který odhalí, že ohlášená riziková událost byla provedena legitimní uživatel.


## <a name="manual-password-reset"></a>Resetování hesla ruční

Pokud nutnost zadat heslo resetovat zásady rizik uživatelů není možné, můžete získat všechny rizikové události pro uživatele uzavřen heslo ruční resetování.

![Resetování hesla](./media/howto-close-active-risk-events/04.png)


Dialogové okno související poskytuje dva různé způsoby vytvoření nového hesla:

![Resetování hesla](./media/howto-close-active-risk-events/05.png)


**Vygenerovat dočasné heslo** -podle vygenerovat dočasné heslo, můžete okamžitě použít identitu zpět do bezpečného stavu. Tato metoda vyžaduje interakci s ovlivnění uživatelé, protože potřebují vědět, co je dočasné heslo. Můžete například odeslat nové dočasné heslo na alternativní e-mailovou adresu uživatele nebo jeho manažerovi. Protože je dočasné heslo, bude uživatel vyzván při dalším přihlášení změnit heslo.


**Vyžadovat, aby uživatel heslo** -samoobslužného obnovení by uživatelé museli resetování hesel umožňuje bez potřeby připojení technickou podporu nebo správce. Stejně jako v případě zásady rizik uživatelů, tato metoda platí jenom pro uživatele, které jsou registrovány pro vícefaktorové ověřování. Pro uživatele, které nebyly byla registrována pro vícefaktorové ověřování tato možnost není dostupná.


## <a name="dismiss-all-risk-events"></a>Skrýt všechny rizikové události

Pokud heslo resetovat není pro vás, může také skrýt všechny rizikové události. 

![Resetování hesla](./media/howto-close-active-risk-events/03.png)

Po kliknutí na **zavřít všechny události**, jsou uzavřeny všechny události a ovlivněného uživatele už nejsou ohrožení. Ale vzhledem k tomu, že tato metoda nemá vliv na stávající heslo, to není vrácení souvisejících identity do bezpečného stavu. Případ použití upřednostňované pro tuto metodu je odstraněného uživatele pomocí aktivní rizikové události. 


## <a name="close-individual-risk-events-manually"></a>Ručně ukončete jednotlivé rizikových událostí

Ručně, můžete zavřít jednotlivých rizikové události. Podle ručnímu zavření rizikových událostí, můžete snížit úroveň rizika pro uživatele. Rizikové události jsou obvykle uzavřít ručně v reakci na související šetření. Například upozorňovat uživatele odhalí, že aktivní riziková událost se nevyžaduje zobrazovat. 
 
Při ruční zavření rizikové události, můžete provést některou z následujících akcí pro změnu stavu rizikové události:

![Akce](./media/howto-close-active-risk-events/06.png)

- **Vyřešit** – Pokud po prozkoumání rizikovou událost, trvalo odpovídajících nápravných akci mimo Identity Protection. proto si myslíte, že by měl za riziková událost zavřená, označte události jako vyřešenou. Vyřešené události se riziková událost stav nastaven na Uzavřeno a rizikové události se už přispívat do uživatelského rizika.

- **Označit jako falešně pozitivní** – v některých případech můžete vyšetřovat rizikovou událost a zjistit, že byla nesprávně označena jako rizikové. Vám může pomoct snížit počet výskytů těchto označením riziková událost jako falešně pozitivní. To vám pomůže zlepšit klasifikace podobné události v budoucnu algoritmech strojového učení. Stav falešně pozitivní událostí je uzavřeno a budou už přispívat do uživatelského rizika.

- **Ignorovat** – Pokud ještě nevstoupilo v libovolné akci nápravy, ale chcete riziková událost odebrat ze seznamu aktivní, můžete označit rizikové události ignorovat a bude uzavřeno stav události. Ignoruje události nepočítají do uživatelského rizika. Tato možnost by měla sloužit pouze v rámci neobvyklé okolnosti.

- **Znovu aktivovat** -rizikových událostí, které nebyly uzavřeny ručně (výběrem vyřešit, falešně positivní nebo ignorovat) můžete znovu aktivovat, nastavení zpět na aktivní stav události. Opětovně rizikových událostí přispívat k výpočtu úroveň rizika uživatele. Rizikové události jsou zavřené prostřednictvím nápravy (například zabezpečené heslo resetovat) nelze aktivovat.
  

## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
