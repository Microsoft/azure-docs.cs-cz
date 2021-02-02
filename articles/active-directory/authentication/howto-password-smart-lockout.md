---
title: Zabránit útokům pomocí inteligentního uzamčení – Azure Active Directory
description: Přečtěte si, jak Azure Active Directory inteligentní uzamčení pomáhá chránit vaši organizaci před útoky hrubou silou, které se pokoušejí uhodnout hesla uživatelů.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255369"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Ochrana uživatelských účtů před útoky pomocí Azure Active Directory inteligentní uzamčení

Inteligentní uzamčení pomáhá uzamknout chybné aktéry, které se snaží odhadnout hesla uživatelů, nebo používat metody hrubou silou k získání. Inteligentní uzamčení dokáže rozpoznávat přihlášení, která pocházejí od platných uživatelů, a považovat je za odlišnou, než u útočníků a jiných neznámých zdrojů. Útočníci se zablokují, zatímco vaši uživatelé budou mít přístup ke svým účtům a budou produktivní.

## <a name="how-smart-lockout-works"></a>Jak funguje inteligentní zamykání

Ve výchozím nastavení zamkne inteligentní zámek účet z pokusů o přihlášení po jednu minutu po 10 neúspěšných pokusech. Po každém následném neúspěšném pokusu o přihlášení se účet zamkne znovu po dobu 1 minuty a v dalších pokusech. Abychom minimalizovali, jak by mohl útočník tento problém obejít, neuzavřeme rychlost, s jakou se období uzamčení šíří nad dalšími neúspěšnými pokusy o přihlášení.

Inteligentní uzamykání sleduje poslední tři chybné hodnoty hash hesla, aby nedošlo k vyššímu zvýšení čítače uzamčení pro stejné heslo. Pokud někdo několikrát zadá stejné chybné heslo, toto chování nezpůsobí, že se účet zamkne.

> [!NOTE]
> Funkce sledování hodnoty hash není dostupná pro zákazníky s povoleným předávacím ověřováním, protože ověřování probíhá místně a v cloudu.

Federované nasazení, která používají AD FS 2016 a AF FS 2019, můžou pomocí [AD FS uzamknutí extranetu a extranetového inteligentního uzamčení](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)povolit podobné výhody.

Inteligentní uzamčení je pro všechny zákazníky Azure AD vždy aktivní a tato výchozí nastavení nabízí správnou kombinaci zabezpečení a použitelnosti. Přizpůsobení nastavení inteligentního uzamčení s hodnotami specifickými pro vaši organizaci vyžaduje pro vaše uživatele Azure AD Premium licence P1 nebo vyšší.

Pomocí inteligentního uzamknutí není zaručeno, že není platný uživatel nikdy uzamčen. Když inteligentní uzamčení uzamkne uživatelský účet, zkusíme, že nebudete muset uzamknout originálního uživatele. Služba uzamčení se pokusí zajistit, aby chybné objekty actor nemohly získat přístup k účtu originálního uživatele. Platí následující důležité informace:

* Každé datové centrum Azure AD sleduje uzamknutí nezávisle. Uživatel má (*threshold_limit * datacenter_count*) počet pokusů, pokud uživatel narazí na každé datové centrum.
* Inteligentní uzamčení používá ke odlišení vadného objektu actor a originálního uživatele známé umístění a neznámé umístění. Neznámá a známá umístění mají samostatné čítače uzamčení.

Inteligentní uzamčení se dá integrovat s hybridními nasazeními, která používají synchronizaci hodnot hash hesel nebo předávací ověřování k ochraně místních účtů Active Directory Domain Services (služba AD DS) před tím, než je útočníci zamkne. Pokud se ve službě Azure AD patřičně nastavují zásady inteligentního zamykání, můžou se útoky odfiltrovat dřív, než se dostanou k místním služba AD DS.

Při použití [předávacího ověřování](../hybrid/how-to-connect-pta.md)platí následující požadavky:

* Prahová hodnota pro uzamčení Azure AD je **nižší** , než prahová hodnota pro uzamčení účtu služba AD DS. Nastavte hodnoty tak, aby prahová hodnota pro uzamčení účtu služba AD DS minimálně dvě nebo třikrát delší než prahová hodnota pro uzamčení Azure AD.
* Doba trvání uzamčení Azure AD musí být nastavená na delší dobu, než je hodnota čítače uzamčení účtu pro nastavení služba AD DS Resetovaná po dobu trvání. Doba trvání Azure AD je nastavená v sekundách, ale doba trvání AD je nastavená v řádu minut.

Například pokud chcete, aby byl čítač Azure AD vyšší než služba AD DS, bude Azure AD 120 sekund (2 minuty), zatímco vaše místní služba AD je nastavená na 1 minutu (60 sekund).

> [!IMPORTANT]
> Správce v současné době nemůže odemknout účty uživatelů cloudu, pokud byly uzamčeny funkcí inteligentního uzamčení. Správce musí počkat na vypršení platnosti doby uzamčení. Uživatel se ale může odemknout pomocí samoobslužného resetování hesla (SSPR) z důvěryhodného zařízení nebo umístění.

## <a name="verify-on-premises-account-lockout-policy"></a>Ověřit zásady uzamčení místních účtů

Pokud chcete ověřit zásady uzamčení místních služba AD DS účtů, proveďte následující kroky ze systému připojeného k doméně s oprávněními správce:

1. Otevřete nástroj pro správu Zásady skupiny.
2. Upravte zásady skupiny, které zahrnují zásady uzamčení účtů vaší organizace, jako je například **výchozí zásada domény**.
3. Přejít na **zásady konfigurace počítače**  >    >  **nastavení systému Windows nastavení**  >  **zabezpečení** zásady  >  **účtů** zásady  >  **uzamčení účtů**.
4. Ověřte **prahovou hodnotu uzamčení účtu** a **Vynulovat čítač uzamčení účtu po** hodnotách.

![Úprava zásad uzamčení místních účtů služby Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Správa hodnot inteligentního uzamčení Azure AD

Na základě požadavků vaší organizace můžete přizpůsobit hodnoty inteligentního uzamčení Azure AD. Přizpůsobení nastavení inteligentního uzamčení s hodnotami specifickými pro vaši organizaci vyžaduje pro vaše uživatele Azure AD Premium licence P1 nebo vyšší.

Chcete-li kontrolovat nebo upravovat hodnoty inteligentního uzamknutí pro vaši organizaci, proveďte následující kroky:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte *Azure Active Directory* a pak vyberte   >  **metody ověřování** zabezpečení  >  **ochrana heslem**.
1. Nastavte **prahovou hodnotu uzamčení** na základě toho, kolik neúspěšných přihlášení na účtu je před prvním uzamčením povolené.

    Výchozí hodnota je 10.

1. Nastavte **dobu uzamčení v sekundách** na délku v sekundách každého uzamknutí.

    Výchozí hodnota je 60 sekund (jedna minuta).

> [!NOTE]
> Pokud se první přihlášení po uzamčení nepovede, účet se znovu odblokuje. V případě opakovaného blokování účtu se zvýší doba trvání uzamčení.

![Přizpůsobení zásady inteligentního uzamčení Azure AD v Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak zjistit, zda funkce inteligentního zamykání funguje

Když se aktivuje prahová hodnota inteligentního uzamčení, při zamčení účtu se zobrazí následující zpráva:

*Váš účet je dočasně zamčený, aby nedocházelo k neoprávněnému použití. Zkuste to znovu později. Pokud pořád máte potíže, obraťte se na správce.*

Při testování inteligentního uzamčení můžou vaše požadavky na přihlášení zpracovávat různá datacentra z důvodu geograficky distribuované a vyrovnávání zatížení služby ověřování Azure AD. V takovém scénáři, protože každé datacentrum Azure AD sleduje uzamčení nezávisle, může trvat více než definovaný prahový počet, který se pokusí o uzamčení způsobit. Uživatel má (*threshold_limit * datacenter_count*) počet špatných pokusů, pokud uživatel narazí na jednotlivá datacentra před tím, než dojde k uzamknutí.

## <a name="next-steps"></a>Další kroky

Pro další přizpůsobení prostředí můžete [nakonfigurovat vlastní zakázaná hesla pro ochranu heslem Azure AD](tutorial-configure-custom-password-protection.md).

Aby uživatelé mohli resetovat nebo měnit heslo z webového prohlížeče, můžete [nakonfigurovat Samoobslužné resetování hesla služby Azure AD](tutorial-enable-sspr.md).
