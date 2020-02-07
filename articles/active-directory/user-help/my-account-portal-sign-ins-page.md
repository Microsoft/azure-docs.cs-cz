---
title: Zobrazení a hledání poslední přihlašovací aktivity na stránce moje přihlášení (Preview) – Azure Active Directory | Microsoft Docs
description: Podrobnosti o tom, jak zobrazit a vyhledat poslední přihlašovací aktivitu na stránce moje přihlášení na portálu Můj účet.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064015"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Zobrazení a hledání poslední přihlašovací aktivity na stránce moje přihlášení (Preview)

Můžete zobrazit veškerou aktivitu přihlášení k pracovnímu nebo školnímu účtu ze stránky **Moje přihlášení** na portálu **můj účet** . Projděte si historii přihlášení, která vám pomůže zkontrolovat neobvyklou aktivitu, která vám pomůže zobrazit:

- Pokud se někdo pokusí uhodnout heslo.

- Pokud se útočník úspěšně přihlásil k vašemu účtu a v jakém umístění.

- K jakým aplikacím se útočník pokusil získat přístup.

## <a name="view-your-recent-sign-in-activity"></a>Zobrazení nedávné přihlašovací aktivity

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak přejít na stránku https://myprofile.microsoft.com/.

2. V levém navigačním podokně vyberte **Moje přihlášení (Preview)** nebo vyberte odkaz **zkontrolovat nedávné aktivity** z bloku **Moje přihlášení (Preview)** .

    ![Stránka můj účet zobrazující zvýrazněné odkazy na nedávné aktivity](media/my-account-portal/my-account-portal-sign-ins.png)

3. Rozbalíte a Prohlédněte si jednotlivé přihlašovací položky a ujistěte se, že každý z nich poznáte. Pokud najdete přihlašovací položku, která nevypadá dobře, důrazně doporučujeme, abyste změnili heslo, abyste mohli lépe chránit svůj účet v případě ohrožení.

    ![Stránka nedávné aktivity s rozšířeným přihlašovacím údajem](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Pokud se zobrazí úspěšné přihlášení

Své vlastní aktivity byste měli rozpoznat jako normální. Pokud si ale všimnete úspěšného přihlášení z neznámého umístění, prohlížeče nebo operačního systému, může to znamenat, že útočník získal přístup k vašemu účtu. V této situaci doporučujeme okamžitě změnit heslo a pak přejít na stránku [bezpečnostní údaje](https://mysignins.microsoft.com/security-info) a aktualizovat nastavení zabezpečení.

Než zjistíte, že něco není správné, ujistěte se, že se nezobrazuje falešně pozitivní (kde položka vypadá s otázkou, ale je v pořádku). Například určíme vaše přibližné umístění a mapu na základě vaší IP adresy. Mobilní sítě jsou obzvláště obtížné přesně určit, protože někdy směrují provoz přes Vzdálená umístění. Pokud jste se tedy přihlásili pomocí svého mobilního zařízení ve státě Washington, umístění může ukazovat přihlášení z Brna. Z tohoto důvodu důrazně doporučujeme, abyste zkontrolovali více podrobností, než jenom umístění. Měli byste taky zajistit, aby měl operační systém, prohlížeč a aplikace smysl.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Pokud se zobrazí neúspěšné přihlášení

Neúspěšné přihlášení bez aktivity relace znamená, že se nezdařila vaše primární metoda ověřování (uživatelské jméno/heslo). To může znamenat, že jste zadali omylem uživatelské jméno nebo heslo, ale může to znamenat, že se útočník pokusil uhodnout heslo. Pokud se domníváte, že by se útočník snažil neúspěšně pokoušet uhodnout heslo, nemusíte měnit heslo, ale důrazně doporučujeme, abyste se zaregistrovali pro Azure Multi-Factor Authentication (MFA). V případě vícefaktorového ověřování, a to i v případě, že hacker nakonec odhadne heslo, nebude mít přístup k vašemu účtu dost.

Pokud se zobrazí neúspěšné přihlášení s poznámkami v části aktivita relace, která říká, že **se další ověření nepovedlo, neplatný kód**, znamená to, že vaše primární ověřování (uživatelské jméno/heslo) bylo úspěšné, ale vícefaktorové ověřování selhalo. V případě, že se jednalo o útočníka, správně vyvolalo heslo, ale stále nebylo možné úspěšně předat výzvu MFA. V takovém případě doporučujeme, abyste pořád změnili heslo, protože útočník získal tuto část napravo a pak přejdete na stránku [bezpečnostní údaje](https://mysignins.microsoft.com/security-info) , kde můžete aktualizovat nastavení zabezpečení.

## <a name="search-for-specific-sign-in-activity"></a>Vyhledat konkrétní přihlašovací aktivitu

Nedávné přihlašovací aktivitu můžete vyhledat pomocí libovolné dostupné informace. Můžete například vyhledat svou poslední přihlašovací aktivitu podle operačního systému, umístění, aplikace a tak dále.

1. Na stránce **revidovat nedávné aktivity** zadejte informace, které chcete vyhledat, do panelu **hledání** . Zadejte například `My Account` a vyhledejte všechny aktivity shromážděné aplikací můj účet.

2. Hledání spustíte tak, že vyberete tlačítko **Hledat** .

    ![Poslední stránka aktivity, která zobrazuje zvýrazněný panel hledání, tlačítko vyhledat a výsledky](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Další kroky

Po zobrazení nedávné přihlašovací aktivity můžete:

- Umožňuje zobrazit nebo spravovat vaše [bezpečnostní údaje](user-help-security-info-overview.md).

- Umožňuje zobrazit nebo spravovat připojená [zařízení](my-account-portal-devices-page.md).

- Umožňuje zobrazit nebo spravovat vaše [organizace](my-account-portal-organizations-page.md).

- Podívejte se, jak vaše organizace [používá data týkající se ochrany osobních údajů](my-account-portal-privacy-page.md).
