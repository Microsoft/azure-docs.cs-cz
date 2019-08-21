---
title: Zabránění útokům hrubou silou pomocí inteligentního uzamčení Azure AD – Azure Active Directory
description: Azure Active Directory Smart zamknutí pomáhá chránit vaši organizaci před útoky hrubou silou, které se pokoušejí uhodnout hesla.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8df01c204a6893f4e2ff1c2ff22fa337d77c519b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637592"
---
# <a name="azure-active-directory-smart-lockout"></a>Inteligentní uzamčení Azure Active Directory

Inteligentní zamykání pomáhá uzamknout chybné aktéry, které se pokoušejí uhodnout hesla uživatelů, nebo používat metody hrubou silou k získání. Dokáže rozpoznávat přihlášení pocházející od platných uživatelů a považovat je za odlišnou od útočníků a jiných neznámých zdrojů. Inteligentní uzamčení odblokuje útočníky, zatímco umožní uživatelům pokračovat v přístupu ke svým účtům a jejich produktivitu.

Ve výchozím nastavení zamkne inteligentní zámek účet z pokusů o přihlášení po jednu minutu po 10 neúspěšných pokusech. Po každém následném neúspěšném pokusu o přihlášení se účet zamkne znovu po dobu 1 minuty a v dalších pokusech.

Inteligentní uzamykání sleduje poslední tři chybné hodnoty hash hesla, aby nedošlo k vyššímu zvýšení čítače uzamčení pro stejné heslo. Pokud někdo několikrát zadá stejné chybné heslo, toto chování nebude mít za následek uzamčení účtu.

 > [!NOTE]
 > Funkce sledování hodnoty hash není dostupná pro zákazníky s povoleným předávacím ověřováním, protože ověřování probíhá místně, ne v cloudu.

Federované nasazení pomocí AD FS 2016 a AF FS 2019 může povolit podobné výhody pomocí [AD FS uzamknutí extranetu a extranetového inteligentního uzamčení](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Inteligentní uzamčení je vždycky zapnuté pro všechny zákazníky Azure AD s těmito výchozími nastaveními, která nabízejí správnou kombinaci zabezpečení a použitelnosti. Přizpůsobení nastavení inteligentního uzamčení s hodnotami specifickými pro vaši organizaci vyžaduje placené licence Azure AD pro vaše uživatele.

Pomocí inteligentního uzamčení není zaručeno, že se originální uživatel nikdy nezamkne. Když inteligentní uzamčení uzamkne uživatelský účet, zkusíme, že nebudete moct tohoto uživatele uzamknout. Služba uzamčení se pokusí zajistit, aby chybné objekty actor nemohly získat přístup k účtu originálního uživatele.  

* Každé Azure Active Directory datové centrum sleduje uzamčení nezávisle. Uživatel bude mít počet pokusů (threshold_limit * datacenter_count), pokud se uživatel dostane do každého datového centra.
* Inteligentní uzamčení používá ke odlišení vadného objektu actor a originálního uživatele známé umístění a neznámé umístění. Neznámá a známá umístění budou mít oddělené čítače uzamčení.

Inteligentní uzamčení se dá integrovat s hybridními nasazeními, a to pomocí synchronizace hodnot hash hesel nebo předávacího ověřování k ochraně místních účtů služby Active Directory před tím, než je útočníci zamkne. Pokud se ve službě Azure AD patřičně nastavují zásady inteligentního zamykání, můžou se útoky filtrovat předtím, než se dostanou k místní službě Active Directory.

Při použití [předávacího ověřování](../hybrid/how-to-connect-pta.md)je potřeba zajistit, aby:

* Prahová hodnota pro uzamčení Azure AD je **nižší** než prahová hodnota pro uzamčení účtu služby Active Directory. Nastavte hodnoty tak, aby prahová hodnota uzamčení účtu služby Active Directory byla minimálně dvě nebo třikrát delší než prahová hodnota pro uzamčení Azure AD. 
* Doba trvání uzamčení Azure AD musí být nastavená na delší dobu, než je čítač uzamčení účtu služby Active Directory pro obnovení po dobu trvání. Počítejte s tím, že doba trvání služby Azure AD je nastavená v sekundách, ale doba trvání AD je nastavená v řádu minut. 

Například pokud chcete, aby byl čítač Azure AD vyšší než AD, pak bude Azure AD 120 sekund (2 minuty), zatímco vaše služba Prem AD je nastavená na 1 minutu (60 sekund).

> [!IMPORTANT]
> Správce v současné době nemůže odemknout účty uživatelů cloudu, pokud byly uzamčeny funkcí inteligentního uzamčení. Správce musí počkat na vypršení platnosti doby uzamčení. Uživatel se ale může odemknout pomocí samoobslužného resetování hesla (SSPR) z důvěryhodného zařízení nebo umístění.

## <a name="verify-on-premises-account-lockout-policy"></a>Ověřit zásady uzamčení místních účtů

Pomocí následujících pokynů ověřte zásady uzamčení místních účtů služby Active Directory:

1. Otevřete nástroj pro správu Zásady skupiny.
2. Upravte zásady skupiny, které obsahují zásady uzamčení účtů vaší organizace, například **výchozí zásady domény**.
3. Přejít > na > zásadyKonfiguracepočítače > nastavení systému Windows nastavení zabezpečenízásadyúčtuuzamčeníúčtu >  >  **Zásady**.
4. Ověřte **prahovou hodnotu uzamčení účtu** a vynulovat **čítač uzamčení účtu po** hodnotách.

![Úprava zásad uzamčení místních účtů služby Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Správa hodnot inteligentního uzamčení Azure AD

Na základě požadavků vaší organizace může být nutné přizpůsobit hodnoty inteligentního uzamknutí. Přizpůsobení nastavení inteligentního uzamčení s hodnotami specifickými pro vaši organizaci vyžaduje placené licence Azure AD pro vaše uživatele.

Chcete-li kontrolovat nebo upravovat hodnoty inteligentního uzamknutí pro vaši organizaci, použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na **Azure Active Directory**a pak na **metody ověřování**.
1. Nastavte **prahovou hodnotu uzamčení**na základě toho, kolik neúspěšných přihlášení na účtu je před prvním uzamčením povolené. Výchozí hodnota je 10.
1. Nastavte **dobu uzamčení v sekundách**na délku v sekundách každého uzamknutí. Výchozí hodnota je 60 sekund (jedna minuta).

> [!NOTE]
> Pokud se první přihlášení po uzamčení nepovede, účet se znovu odblokuje. V případě opakovaného blokování účtu se zvýší doba trvání uzamčení.

![Přizpůsobení zásady inteligentního uzamčení Azure AD v Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak zjistit, zda funkce inteligentního zamykání funguje

Když se aktivuje prahová hodnota inteligentního uzamčení, při zamčení účtu se zobrazí následující zpráva:

**Váš účet je dočasně zamčený, aby nedocházelo k neoprávněnému použití. Zkuste to znovu později. Pokud pořád máte potíže, obraťte se na správce.**

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak zakázat chybná hesla ve vaší organizaci pomocí Azure AD.](howto-password-ban-bad.md)
* [Nakonfigurujte Samoobslužné resetování hesla, aby uživatelé mohli odemknout svoje vlastní účty.](quickstart-sspr.md)
