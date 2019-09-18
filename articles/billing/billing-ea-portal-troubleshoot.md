---
title: Řešení potíží s přístupem k portálu Azure EA
description: Tento článek popisuje některé běžné problémy, které mohou nastat u smlouvy Azure Enterprise (EA) na portálu Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: troubleshooting
ms.service: billing
manager: boalcsva
ms.openlocfilehash: a2214ef0dd3b7c4f706ebbb529b6c44e956f9242
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900875"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Řešení potíží s přístupem k portálu Azure EA

Tento článek popisuje některé běžné problémy, které mohou nastat u smlouvy Azure Enterprise (EA). Portál Azure EA slouží ke správě uživatelů a nákladů v rámci smlouvy Enterprise. Při konfiguraci nebo aktualizaci přístupu k portálu Azure EA, může dojít k následujícím potížím.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problémy při přidávání správce do registrace

U registrací v rámci smlouvy Enterprise existují různé typy úrovní ověřování. Pokud jsou úrovně ověřování nastaveny nesprávně, může při pokusu o přihlášení k portálu Azure EA docházet k problémům.

Portál Azure EA slouží k udělení přístupu uživatelům s různými úrovněmi ověřování. Podnikový správce může úrovně ověřování aktualizovat tak, aby splňovaly požadavky na zabezpečení v dané organizaci.

### <a name="authentication-level-types"></a>Typy úrovně ověřování

- Jenom účet Microsoft: Pro organizace, které chtějí používat, vytvářet a spravovat uživatele prostřednictvím účtů Microsoft
- Pracovní nebo školní účet: Pro organizace, které si nastavily službu Active Directory s federací na cloud a budou mít všechny účty jsou v jednom tenantovi
- Pracovní nebo školní účet napříč tenanty: Pro organizace, které si nastavily službu Active Directory s federací na cloud a budou mít účty ve více tenantech
- Smíšený účet: Umožňuje přidávat uživatele s účtem Microsoft nebo pracovním či školním účtem (případně oběma typy účtů).

První pracovní nebo školní účet přidaný do registrace určí _výchozí_ nebo _hlavní_ doménu. Pokud budete chtít přidat pracovní nebo školní účet s jiným tenantem, musíte úroveň ověřování u registrace změnit na ověřování napříč tenanty.

Aktualizace úrovně ověřování:

1. Přihlaste se na portál Azure EA jako podnikový správce.
2. Na levém navigačním panelu klikněte na **Spravovat**.
3. Klikněte na kartu**Registrace**.
4. V části **Podrobnosti registrace** vyberte **Úroveň ověřování**.
5. Klikněte na symbol tužky.
6. Klikněte na **Uložit**.

![Příklad znázorňující úrovně ověřování ](./media/billing-ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Účty Microsoft musí mít přidružené ID vytvořené na webu [https://signup.live.com](https://signup.live.com/).

Pracovní nebo školní účty jsou dostupné pro organizace, které si nastavily službu Active Directory s federací a všechny účty jsou v jednom tenantovi. Pokud je interní služba Active Directory dané společnosti federovaná, je možné uživatele přidávat s federovaným ověřováním prostřednictvím jejich pracovních nebo školních účtů.

Pokud vaše organizace federaci služby Active Directory nepoužívá, nemůžete svou pracovní nebo školní e-mailovou adresu použít. Místo toho si zaregistrujte nebo vytvořte novou e-mailovou adresu a zaregistrujte ji jako účet Microsoft.

## <a name="unable-to-access-the-azure-ea-portal"></a>Nejde získat přístup na portál Azure EA

Pokud se vám při pokusu o přihlášení k portálu Azure EA zobrazí chybová zpráva, použijte následující kroky pro řešení potíží:

- Ujistěte se, že používáte správnou adresu URL portálu Azure EA: https://ea.azure.com.
- Zjistěte si, jestli vám byl přístup k portálu Azure EA zřízen na základě pracovního či školního účtu, nebo účtu Microsoft.
  - Pokud používáte pracovní účet, zadejte svůj pracovní e-mail a heslo. Toto heslo vám poskytne vaše organizace. V případě, že s ním máte problémy, můžete se v oddělení IT zeptat, jak ho resetovat.
  - Pokud používáte účet Microsoft, zadejte odpovídající e-mailovou adresu a heslo. Pokud jste heslo ke svému účtu Microsoft zapomněli, můžete si ho resetovat na stránce [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Přihlaste se prostřednictvím anonymní relace prohlížeče (např. InPrivate), ve které se nezachovávají žádné soubory cookie ani informace uložené v mezipaměti z předchozích nebo aktuálních relací. Vymažte mezipaměť prohlížeče a k otevření stránky https://ea.azure.com použijte anonymní okno prohlížeče (např. InPrivate).
- Pokud se vám při použití účtu Microsoft zobrazí chyba _Neplatný uživatel_, může to být způsobeno tím, že máte více účtů Microsoft. Ten, ke kterému se pokoušíte přihlásit, nepředstavuje primární e-mailovou adresu.
Případně může být příčinou chyby _Neplatný uživatel_ to, že při přidávání uživatele do registrace byl použit nesprávný typ účtu. Například pracovní nebo školní účet namísto účtu Microsoft. V tomto příkladu máte dalšího správce EA – přidejte správný účet, jinak budete muset kontaktovat [podporu](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).

## <a name="no-activation-email-received"></a>Nepřišel aktivační e-mail

Aktivační e-mail z portálu Azure EA se odesílá z adresy *waep@microsoft.com* . Pokud jste aktivační e-mail neobdrželi, vyhledejte ho ve složce se spamem nebo nevyžádanou poštou. Zpráva je v předmětu označená jako _pozvánka k zobrazení nebo správě služeb Microsoft Azure_. Měla by se odeslat každému nově přidanému správci EA.

Pokud jste si jisti, že vás někdo nastavil jako správce EA, nemusíte čekat, až dostanete aktivační e-mail, abyste se mohli k portálu Azure EA přihlásit. Místo toho můžete přejít na https://ea.azure.com a přihlásit se pomocí své e-mailové adresy a hesla (pro pracovní či školní účet nebo účet Microsoft).

Pokud si potřebujete zkontrolovat primární alias, přejděte na [https://account.live.com](https://account.live.com). Pak klikněte na **Vaše informace** a na **Spravovat způsob přihlášení k Microsoftu**. Podle pokynů ověřte alternativní e-mailovou adresu a získejte kód pro přístup k citlivým informacím. Zadejte tento bezpečnostní kód. Pokud nechcete nastavit dvojúrovňové ověřování, vyberte **Nastavit později**.

Zobrazí se stránka **Spravovat způsob přihlášení k Microsoftu**, na které uvidíte aliasy svých účtů. Ověřte, že se k portálu Azure EA přihlašujete pomocí primárního aliasu. Pokud se přihlašujete pomocí jiného aliasu, můžete ho nastavit jako svůj primární. Nebo můžete místo něho použít primární alias pro portál Azure EA.

## <a name="next-steps"></a>Další kroky

- Správci na portálu Azure EA by si měli přečíst téma [Správa portálu Azure EA](billing-ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
