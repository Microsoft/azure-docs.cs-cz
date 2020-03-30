---
title: Zobrazení a vyhledávání poslední přihlašovací aktivity na stránce Moje přihlášení (preview) – Azure Active Directory | Dokumenty společnosti Microsoft
description: Podrobnosti o zobrazení a prohledávaní nedávné přihlašovací aktivity na stránce Moje přihlášení na portálu Můj účet.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064015"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Zobrazení a vyhledávání poslední přihlašovací aktivity na stránce Moje přihlášení (preview)

Všechny nedávné aktivity přihlášení k pracovnímu nebo školnímu účtu si můžete zobrazit na stránce **Moje přihlášení** na portálu **Můj účet.** Kontrola historie přihlášení vám pomůže zkontrolovat neobvyklou aktivitu tím, že vám pomůže zjistit:

- Pokud se někdo snaží uhodnout vaše heslo.

- Pokud se útočník úspěšně přihlásil k vašemu účtu a z jakého místa.

- K jakým aplikacím se útočník pokusil získat přístup.

## <a name="view-your-recent-sign-in-activity"></a>Zobrazení nedávné přihlašovací aktivity

1. Přihlaste se ke svému pracovnímu https://myprofile.microsoft.com/ nebo školnímu účtu a přejděte na stránku.

2. V levém navigačním podokně vyberte **Moje přihlášení (náhled)** nebo vyberte odkaz **Zkontrolovat poslední aktivitu** z bloku **Moje přihlášení (náhled).**

    ![Stránka Můj účet se zvýrazněnými odkazy Na poslední aktivitu](media/my-account-portal/my-account-portal-sign-ins.png)

3. Rozbalte a zkontrolujte každou z položek přihlášení a ujistěte se, že je poznáváte. Pokud najdete položku přihlášení, která vám nepřipadá povědomá, důrazně doporučujeme změnit heslo, abyste ochránili svůj účet, pokud byl napaden.

    ![Stránka poslední aktivity s rozšířenými přihlašovacími údaji](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Pokud se zobrazí úspěšné přihlášení

Měli byste rozpoznat svou vlastní činnost jako normální. Pokud však zaznamenáte úspěšné přihlášení z cizího umístění, prohlížeče nebo operačního systému, může to znamenat, že útočník získal přístup k vašemu účtu. V takovém případě doporučujeme okamžitě změnit heslo a potom přejděte na stránku [Bezpečnostní údaje](https://mysignins.microsoft.com/security-info) a aktualizujte nastavení zabezpečení.

Než zjistíte, že něco je nesprávné, ujistěte se, že nevidíte falešně pozitivní (kde položka vypadá pochybně, ale je v pořádku). Například určíme vaši přibližnou polohu a mapu na základě vaší IP adresy. Mobilní sítě jsou obzvláště těžké určit, protože někdy směrují provoz přes vzdálená místa. Pokud jste se tedy přihlásili pomocí mobilního zařízení ve státě Washington, může se na místě zobrazit přihlášení z Kalifornie. Z tohoto důvodu důrazně doporučujeme zkontrolovat další podrobnosti, kromě místa. Měli byste se také ujistit, že operační systém, prohlížeč a aplikace mají také smysl.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Pokud se zobrazí neúspěšné přihlášení

Neúspěšné přihlášení bez aktivity relace znamená, že primární metoda ověření (uživatelské jméno/heslo) se nezdařila. To může znamenat, že jste nesprávně zadali své uživatelské jméno nebo heslo, ale může to také znamenat, že se útočník pokoušel uhodnout vaše heslo. Pokud se domníváte, že se útočník pokoušel neúspěšně uhodnout vaše heslo, nemusíte heslo měnit, ale důrazně doporučujeme, abyste se zaregistrovali pro Azure Multi-Factor Authentication (MFA). S Vícefaktorové ověřování, i když hacker nakonec uhádne vaše heslo, nebude to stačit pro přístup k vašemu účtu.

Pokud se zobrazí neúspěšné přihlášení, s poznámkou v rámci aktivity relace, která říká, **další ověření se nezdařilo, neplatný kód**, znamená to, že primární ověřování (uživatelské jméno/heslo) proběhlo úspěšně, ale vícefaktorové ověřování se nezdařilo. Pokud se jednalo o útočníka, správně uhodl heslo, ale stále nemohli projít výzvou vícefaktorové ověřování. V takovém případě doporučujeme, abyste heslo stále měnili, protože útočník tuto část správně získal, a potom přejděte na stránku [Informace o zabezpečení](https://mysignins.microsoft.com/security-info) a aktualizujte nastavení zabezpečení.

## <a name="search-for-specific-sign-in-activity"></a>Hledání konkrétní přihlašovací aktivity

Svou poslední aktivitu přihlášení můžete prohledávat podle kterékoli z dostupných informací. Můžete například vyhledat svou poslední aktivitu přihlášení podle operačního systému, umístění, aplikace a tak dále.

1. Na stránce **Zkontrolovat poslední aktivitu** zadejte informace, které chcete vyhledat, do panelu **Hledat.** Zadejte `My Account` například vyhledat všechny aktivity shromážděné aplikací Můj účet.

2. Chcete-li začít hledat, vyberte tlačítko **Hledat.**

    ![Stránka Nedávná aktivita se zvýrazněným vyhledávacím panelem, tlačítkem hledání a výsledky](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Další kroky

Po zobrazení nedávné přihlašovací aktivity můžete:

- Zobrazení nebo správa [bezpečnostních údajů](user-help-security-info-overview.md).

- Zobrazení nebo správa [připojených zařízení](my-account-portal-devices-page.md).

- Zobrazení nebo správa [organizací](my-account-portal-organizations-page.md).

- Zobrazení způsobu, jakým vaše organizace [používá vaše data týkající se ochrany osobních údajů](my-account-portal-privacy-page.md).
