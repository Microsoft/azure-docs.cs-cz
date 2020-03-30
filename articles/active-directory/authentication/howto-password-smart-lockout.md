---
title: Předcházení útokům pomocí inteligentního uzamčení – Služba Azure Active Directory
description: Inteligentní uzamčení azure služby Azure Active Directory pomáhá chránit vaši organizaci před útoky hrubou silou, které se snaží uhodnout hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd49a4429dc0d7f5db07a577016c21de8fc58d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75762871"
---
# <a name="azure-active-directory-smart-lockout"></a>Inteligentní uzamykání Azure Active Directory

Inteligentní uzamčení pomáhá při zamykání špatných herců, kteří se pokoušejí uhodnout hesla uživatelů nebo použít metody hrubé síly, aby se dostali dovnitř. Dokáže rozpoznat přihlášení od platných uživatelů a zacházet s nimi jinak než útočníci a jiné neznámé zdroje. Inteligentní uzamčení uzamkne útočníky a zároveň umožní uživatelům pokračovat v přístupu ke svým účtům a být produktivní.

Ve výchozím nastavení inteligentní uzamčení uzamkne účet z pokusů o přihlášení po dobu jedné minuty po 10 neúspěšných pokusech. Účet se znovu uzamkne po každém následném neúspěšném pokusu o přihlášení, nejprve po dobu jedné minuty a déle v následných pokusech.

Inteligentní uzamčení sleduje poslední tři chybné zapisování hesel, aby se zabránilo zvýšení čítače uzamčení pro stejné heslo. Pokud někdo zadá stejné chybné heslo vícekrát, toto chování nezpůsobí uzamčení účtu.

 > [!NOTE]
 > Funkce sledování hash není k dispozici pro zákazníky s předávacím ověřováním povoleným, protože ověřování probíhá místně, které není v cloudu.

Federovaná nasazení pomocí ad FS 2016 a AF FS 2019 mohou povolit podobné výhody pomocí [uzamčení extranetové služby AD FS a inteligentního uzamčení extranetu](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Inteligentní uzamčení je vždy zapnuto pro všechny zákazníky Azure AD s těmito výchozími nastaveními, které nabízejí správnou kombinaci zabezpečení a použitelnosti. Přizpůsobení nastavení inteligentního uzamčení s hodnotami specifickými pro vaši organizaci vyžaduje pro vaše uživatele placené licence Azure AD.

Použití inteligentního uzamčení nezaručuje, že skutečný uživatel nebude nikdy uzamčen. Když inteligentní uzamčení uzamkne uživatelský účet, snažíme se co nejlépe nezamykat skutečného uživatele. Služba uzamčení se pokusí zajistit, že chybní uživatelé nemohou získat přístup k skutečnému uživatelskému účtu.  

* Každé datové centrum Služby Azure Active Directory sleduje uzamčení nezávisle. Uživatel bude mít (threshold_limit * datacenter_count) počet pokusů, pokud uživatel narazí na každé datové centrum.
* Inteligentní uzamčení používá známé umístění vs neznámé umístění rozlišovat mezi špatný herec a skutečný uživatel. Neznámá a známá umístění budou mít samostatné čítače uzamčení.

Inteligentní uzamčení lze integrovat s hybridními nasazeními pomocí synchronizace hash hesel nebo předávacího ověřování, které chrání místní účty služby Active Directory před uzamčením útočníky. Nastavením zásad inteligentního uzamčení ve službě Azure AD odpovídajícím způsobem lze útoky odfiltrovat dříve, než se dostanou k místnímu službě Active Directory.

Při použití [předávacího ověřování](../hybrid/how-to-connect-pta.md)je třeba se ujistit, že:

* Prahová hodnota uzamčení služby Azure AD je **menší** než prahová hodnota uzamčení účtu služby Active Directory. Nastavte hodnoty tak, aby prahová hodnota uzamčení účtu služby Active Directory byla alespoň dvakrát nebo třikrát delší než prahová hodnota uzamčení služby Azure AD. 
* Doba uzamčení služby Azure AD musí být nastavena delší než čítač uzamčení účtu resetování služby Active Directory po trvání. Uvědomte si, že doba trvání Azure AD je nastavena v sekundách, zatímco doba trvání služby AD je nastavena v minutách. 

Například pokud chcete, aby vaše čítač Azure AD být vyšší než AD, pak Azure AD by 120 sekund (2 minuty), zatímco místní služba AD je nastavena na 1 minutu (60 sekund).

> [!IMPORTANT]
> V současné době správce nemůže odemknout cloudové účty uživatelů, pokud byly uzamčeny pomocí funkce Inteligentní uzamčení. Správce musí počkat na vypršení doby uzamčení. Uživatel však může odemknout pomocí samoobslužné resetování hesla (SSPR) z důvěryhodného zařízení nebo umístění.

## <a name="verify-on-premises-account-lockout-policy"></a>Ověření zásad uzamčení místního účtu

K ověření místních zásad uzamčení účtu služby Active Directory použijte následující pokyny:

1. Otevřete nástroj správa zásad skupiny.
2. Upravte zásady skupiny, které zahrnují zásady uzamčení účtu vaší organizace, například **výchozí zásady domény**.
3. Přejděte do**části Zásady** > **Windows Settings** >  **uzamčení** > účtu nastavení**systému** > Windows Nastavení**nastavení** > **účtu**.
4. Po hodnotách ověřte **prahovou hodnotu uzamčení účtu** a **vynulovacíčí čítač uzamčení účtu.**

![Úprava místní zásady uzamčení účtu služby Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Správa hodnot inteligentního uzamčení azure a služby Azure AD

Na základě vašich organizačních požadavků může být nutné přizpůsobit hodnoty inteligentního uzamčení. Přizpůsobení nastavení inteligentního uzamčení s hodnotami specifickými pro vaši organizaci vyžaduje pro vaše uživatele placené licence Azure AD.

Chcete-li zkontrolovat nebo upravit hodnoty inteligentního uzamčení pro vaši organizaci, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyhledejte a vyberte *Azure Active Directory*. Vyberte**metody** > ověřování **zabezpečení** > **Ochrana heslem**.
1. Nastavte **prahovou hodnotu uzamčení**na základě toho, kolik neúspěšných přihlášení je povoleno na účtu před jeho prvním uzamčením. Výchozí hodnota je 10.
1. Nastavte **dobu uzamčení v sekundách**, na délku v sekundách každého uzamčení. Výchozí hodnota je 60 sekund (jedna minuta).

> [!NOTE]
> Pokud první přihlášení po uzamčení také selže, účet se znovu uzamkne. Pokud účet uzamkne opakovaně, doba uzamčení se prodlužuje.

![Přizpůsobení zásad inteligentního uzamčení Azure AD na webu Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak zjistit, zda funkce Smart lockout funguje nebo ne

Když se aktivuje prahová hodnota inteligentního uzamčení, zobrazí se při uzamčení účtu následující zpráva:

**Váš účet je dočasně uzamčen, aby se zabránilo neoprávněnému použití. Zkuste to znovu později a pokud máte stále potíže, obraťte se na správce.**

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak zakázat špatná hesla ve vaší organizaci pomocí Azure AD.](howto-password-ban-bad.md)
* [Nakonfigurujte samoobslužné resetování hesla, aby uživatelé mohli odemknout své vlastní účty.](quickstart-sspr.md)
