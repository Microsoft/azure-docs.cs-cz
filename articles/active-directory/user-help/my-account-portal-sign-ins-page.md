---
title: Zobrazení a hledání poslední přihlašovací aktivity na stránce moje přihlášení – Azure Active Directory | Microsoft Docs
description: Podrobnosti o tom, jak zobrazit a vyhledat poslední přihlašovací aktivitu na stránce moje přihlášení na portálu Můj účet.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096068"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Zobrazení a hledání poslední přihlašovací aktivity na stránce moje přihlášení

Můžete zobrazit veškerou aktivitu přihlášení k pracovnímu nebo školnímu účtu ze stránky **Moje přihlášení** na portálu **můj účet** . Projděte si historii přihlášení, která vám pomůže zkontrolovat neobvyklou aktivitu, která vám pomůže zobrazit:

- Pokud se někdo pokusí uhodnout heslo.
- Pokud se útočník úspěšně přihlásil k vašemu účtu a v jakém umístění.
- K jakým aplikacím se útočník pokusil získat přístup.

>[!Note]
> Pokud se vám při přihlašování pomocí osobního účet Microsoft zobrazí chyba, můžete se přihlásit pomocí názvu domény vaší organizace (například contoso.com) nebo **ID tenanta** vaší organizace od správce v jedné z následujících adres URL:
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>Zobrazení nedávné přihlašovací aktivity

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak přejít na https://myaccount.microsoft.com/ stránku.

2. V levém navigačním podokně vyberte **Moje přihlášení** nebo vyberte odkaz **zkontrolovat nedávné aktivity** z bloku **Moje přihlášení** .

    ![Stránka můj účet zobrazující zvýrazněné odkazy na nedávné aktivity](media/my-account-portal/my-account-portal-sign-ins.png)

3. Rozbalíte a Prohlédněte si jednotlivé přihlašovací položky a ujistěte se, že každý z nich poznáte. Pokud najdete přihlašovací položku, která nevypadá dobře, změňte heslo pro ochranu účtu v případě ohrožení bezpečnosti.

    ![Stránka nedávné aktivity s rozšířeným přihlašovacím údajem](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Pokud se zobrazí úspěšné přihlášení

Při kontrole vlastní běžné přihlašovací aktivity se někdy může zobrazit úspěšné přihlášení z neznámého umístění, prohlížeče nebo operačního systému. Neznámé přihlášení může znamenat, že útočník získal přístup k vašemu účtu. Pokud se zobrazí aktivita, kterou jste nepovolili, doporučujeme vám okamžitě změnit heslo a pak přejít na [informace o zabezpečení](https://mysignins.microsoft.com/security-info) a aktualizovat nastavení zabezpečení.

Než zjistíte, že něco není správné, ujistěte se, že se nezobrazuje falešně pozitivní (kde položka vypadá s otázkou, ale je v pořádku). Například určíme vaše přibližné umístění a mapu na základě vaší IP adresy. Mobilní sítě jsou obzvláště obtížné přesně určit, protože někdy směrují provoz přes Vzdálená umístění. I když se přihlašujete pomocí svého mobilního zařízení ve státě Washington, umístění může zobrazit přihlášení z Brna. Důrazně doporučujeme, abyste zkontrolovali podrobnosti nad rámec pouze místa. Zajistěte, aby měl operační systém, prohlížeč a aplikace smysl.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Pokud se zobrazí neúspěšné přihlášení

Pokud se zobrazí neúspěšné přihlášení, může to znamenat, že jste zadali přihlašovací údaje chybně. Může to také znamenat, že se útočník pokouší uhodnout heslo. Pokud chcete na toto riziko odpovědět, nemusíte měnit heslo, ale doporučujeme, abyste se zaregistrovali pro Azure AD Multi-Factor Authentication (MFA). Při ověřování pomocí služby Multi-Factor Authentication, a to i v případě, že počítačový podvodník heslo vymění, nebude pro přístup k účtu dost místa.

![Neúspěšná dlaždice přihlášení](media/my-account-portal-sign-ins-page/unsuccessful.png)

Pokud se zobrazí neúspěšné přihlášení s poznámkou v **aktivitě relace** , znamená to, `Additional verification failed, invalid code` že vaše primární přihlašovací údaje se úspěšně provedly, ale ověřování službou Multi-Factor Authentication se nezdařilo. Tento stav může znamenat, že útočník správně vyvolal vaše heslo, ale nedokázal předat výzvu služby Multi-Factor Authentication. Doporučujeme, abyste si pořád změnili heslo, protože ho už mohl mít, a přejít na stránku [informace o zabezpečení](https://mysignins.microsoft.com/security-info) , kde můžete aktualizovat nastavení zabezpečení.

## <a name="search-for-specific-sign-in-activity"></a>Vyhledat konkrétní přihlašovací aktivitu

Nedávné přihlašovací aktivitu můžete vyhledat pomocí libovolné dostupné informace. Můžete například vyhledat svou poslední přihlašovací aktivitu podle operačního systému, umístění, aplikace a tak dále.

1. Na stránce **revidovat nedávné aktivity** zadejte informace, které chcete vyhledat, do panelu **hledání** . Zadejte například, pokud `Unsuccessful` chcete vyhledat veškerou neúspěšnou přihlašovací aktivitu shromážděnou aplikací můj účet.

2. Hledání spustíte tak, že vyberete tlačítko **Hledat** .

    ![Poslední stránka aktivity, která zobrazuje zvýrazněný panel hledání, tlačítko vyhledat a výsledky](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Potvrdit neobvyklou aktivitu

Přihlášení, která jsou označena jako neobvyklá aktivita, lze potvrdit na dlaždici pro danou aktivitu na stránce **Moje přihlášení** .

![Neobvyklá přihlašovací dlaždice pro potvrzení, že jste provedli nebo jste se nepokusili o přihlášení](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Další kroky

Po zobrazení nedávné přihlašovací aktivity můžete:

- Umožňuje zobrazit nebo spravovat vaše [bezpečnostní údaje](./security-info-setup-signin.md).

- Umožňuje zobrazit nebo spravovat připojená [zařízení](my-account-portal-devices-page.md).

- Umožňuje zobrazit nebo spravovat vaše [organizace](my-account-portal-organizations-page.md).

- Podívejte se, jak vaše organizace [používá data týkající se ochrany osobních údajů](my-account-portal-privacy-page.md).

- Změna [nastavení portálu moje účet](my-account-portal-settings.md)