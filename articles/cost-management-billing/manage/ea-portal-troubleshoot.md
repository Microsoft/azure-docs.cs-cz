---
title: Řešení potíží s přístupem k portálu Azure EA
description: Tento článek popisuje některé běžné problémy, které mohou nastat u smlouvy Azure Enterprise (EA) na portálu Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 5402ee3283e5ae11f43884b4002486e8bdefae2c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294403"
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

První pracovní nebo školní účet přidaný do registrace určuje _výchozí_ doménu. Pokud budete chtít přidat pracovní nebo školní účet s jiným tenantem, musíte úroveň ověřování u registrace změnit na ověřování napříč tenanty.

Aktualizace úrovně ověřování:

1. Přihlaste se na portál Azure EA jako podnikový správce.
2. Na levém navigačním panelu klikněte na **Spravovat**.
3. Klikněte na kartu**Registrace**.
4. V části **Podrobnosti registrace** vyberte **Úroveň ověřování**.
5. Klikněte na symbol tužky.
6. Klikněte na **Uložit**.

![Příklad znázorňující úrovně ověřování ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

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
- Pokud se vám při použití účtu Microsoft zobrazí chyba _Neplatný uživatel_, může to být způsobené tím, že máte víc účtů Microsoft. Ten, ke kterému se pokoušíte přihlásit, nepředstavuje primární e-mailovou adresu.
Případně může být příčinou chyby _Neplatný uživatel_ to, že při přidávání uživatele do registrace byl použit nesprávný typ účtu. Například pracovní nebo školní účet namísto účtu Microsoft. V tomto příkladu máte dalšího správce EA – přidejte správný účet, jinak budete muset kontaktovat [podporu](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Pokud si potřebujete zkontrolovat primární alias, přejděte na [https://account.live.com](https://account.live.com). Pak klikněte na **Vaše informace** a na **Spravovat způsob přihlášení k Microsoftu**. Podle pokynů ověřte alternativní e-mailovou adresu a získejte kód pro přístup k citlivým informacím. Zadejte tento bezpečnostní kód. Pokud nechcete nastavit dvojúrovňové ověřování, vyberte **Nastavit později**.
  - Zobrazí se stránka **Správa způsobu přihlášení k Microsoftu**, na které uvidíte aliasy svých účtů. Ověřte, že se k portálu Azure EA přihlašujete pomocí primárního aliasu. Pokud se přihlašujete pomocí jiného aliasu, můžete ho nastavit jako svůj primární. Nebo můžete místo něho použít primární alias pro portál Azure EA.

## <a name="azure-ea-activation-faq"></a>Nejčastější dotazy k aktivaci Azure EA

Tato část článku popisuje řešení běžných problémů souvisejících s aktivací Azure EA.

### <a name="i-didnt-receive-an-activation-email"></a>Nepřišel mi aktivační e-mail

Aktivační e-mail z portálu Azure EA se odesílá z adresy *waep@microsoft.com* . Pokud jste aktivační e-mail neobdrželi, vyhledejte ho ve složce se spamem nebo nevyžádanou poštou. Zpráva je v předmětu označená jako _pozvánka k zobrazení nebo správě služeb Microsoft Azure_. Měla by se odeslat každému nově přidanému správci EA.

Pokud jste si jisti, že vás někdo nastavil jako správce EA, nemusíte čekat, až dostanete aktivační e-mail, abyste se mohli k portálu Azure EA přihlásit. Místo toho můžete přejít na https://ea.azure.com a přihlásit se pomocí své e-mailové adresy a hesla (pro pracovní či školní účet nebo účet Microsoft).

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Chci ke své registraci přidat nového správce EA

Nového podnikového správce můžou přidat stávající podnikoví správci. Pokud jste správcem EA, přihlaste se k portálu EA, klikněte na **Spravovat**, pak v pravém horním rohu klikněte na **+ Přidat správce** a přidejte nového správce EA. Ujistěte se, že znáte e-mailovou adresu a upřednostňovanou metodu přihlašování (např. prostřednictvím pracovního nebo školního účtu nebo účtu Microsoft Live ID) uživatelů, abyste je mohli přidat.

Pokud nejste správcem EA, obraťte se na správce EA ve vaší společnosti a požádejte je, aby vás k registraci přidali. Jakmile vás k registraci přidají, obdržíte aktivační e-mail.

Pokud vám však správci EA nemůžou pomoct, můžeme vás přidat jejich jménem, když nám poskytnete následující informace:
- Číslo registrace
- E-mailová adresa, která se má přidat, a typ ověřování (pracovní nebo školní účet nebo účet Microsoft)
- E-mailové schválení od správce EA

Jakmile budete mít všechny požadované informace, odešlete žádost na stránce [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Chci v registraci aktualizovat prvního správce EA

Prvního správce EA je možné aktualizovat na webu Volume Licensing Service Center aktualizací kontaktu pro zasílání oznámení a online správce na portálu. Aktualizace portálu EA bude trvat přibližně 24 hodin. Po aktualizaci nový správce EA obdrží aktivační e-mail.

Pokud nemáte přístup k portálu VLSC nebo pokud váš původní správce EA už nemůže spravovat registraci a nemá přístup k portálu EA, na stránce [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) odešlete žádost o aktualizaci a zadejte následující informace:
- Číslo registrace
- E-mailová adresa, která se má přidat, a typ ověřování (pracovní nebo školní účet nebo účet Microsoft)
- Důvod změny původního správce EA
- E-mailové schválení od původního správce EA

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>Aktuální správce EA už u společnosti nepracuje

Registrace EA může mít několik správců EA. S žádostí o přidání nového správce EA, vlastníka účtu nebo správce oddělení se tedy můžete obrátit na jiného správce EA. Pokud si však nejste jisti, kdo je ve vaší společnosti správcem EA, nebo pokud v dané registraci není k dispozici žádný jiný správce EA, obraťte se na nás s následujícími informacemi:
- Číslo registrace
- E-mailová adresa, která se má přidat, a typ ověřování (pracovní nebo školní účet nebo účet Microsoft)
- Informace o tom, že aktuální správce EA už u společnosti nepracuje

Upozorňujeme, že pokud jsou v dané registraci další správci EA, obrátíme se na ně s žádostí o schválení správních změn v registraci.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>Moje registrace se zobrazuje ve stavu čekání na vyřízení. Jak můžu registraci aktivovat?

Registrace jsou ve stavu čekání na vyřízení v případě, že se původní správce EA ještě k registraci nepřihlásil. Pokud jste správcem EA, přihlaste se k portálu Azure EA. Na úvodní stránce s čísly všech vašich registrací se možná vaše registrace čekající na vyřízení nezobrazí. V pravém horním rohu webu EA Portal zrušte zaškrtnutí políčka Aktivní, aby se zobrazila i registrace čekající na vyřízení. Kliknutím na příslušnou registraci přejděte k informacím o registraci. Jakmile se dostanete na stránku Správa registrace, stav Čeká na vyřízení se aktualizuje na Aktivní.

### <a name="why-is-my-account-stuck-in-pending-status"></a>Proč se můj účet zaseknul ve stavu čekání na vyřízení?

Při prvním přidání nových vlastníků účtů k registraci se u nich vždy zobrazí stav Čeká na vyřízení. Jakmile obdrží úvodní aktivační e-mail, můžou se vlastníci účtu přihlásit a aktivovat svůj účet. Po přihlášení se stav účtu aktualizuje z Čeká na vyřízení na Aktivní.

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Při přihlašování k portálu Azure EA došlo k chybě

Během přihlašování k portálu Azure EA se může zobrazit chybová zpráva z několika důvodů. Postupujte podle těchto kroků pro řešení potíží:

 1. Ujistěte se, že používáte správnou adresu URL portálu EA: [https://ea.azure.com](https://ea.azure.com).
 1. Zjistěte si, jestli pro přístup k portálu Azure EA máte používat pracovní nebo školní účet, nebo účet Microsoft Live ID. Pokud používáte pracovní účet, zadejte svůj pracovní e-mail a heslo. Pokud používáte účet Microsoft Live ID, zadejte e-mail a heslo svého účtu Microsoft Live ID. Pokud jste zapomněli heslo svého účtu Microsoft Live ID, resetujte si ho na stránce [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. Doporučujeme k přihlášení použít privátní režim prohlížeče, ve kterém se neuchovávají soubory cookie ani mezipaměť z předchozích ani stávajících relací. Vymažte mezipaměť a v privátním režimu nebo anonymním okně otevřete stránku [https://ea.azure.com](https://ea.azure.com).
 1. Pokud používáte účet Microsoft a dochází k chybě Neplatný uživatel, může být důvodem to, že máte více účtů Microsoft a účet, pomocí kterého se pokoušíte přihlásit, není primárním aliasem. Pokud chcete zkontrolovat primární alias, přejděte na adresu account.live.com.
    - Přejděte do části Vaše informace a vyberte Spravovat přihlašovací e-mail nebo telefonní číslo.
    - Podle pokynů na obrazovce ověřte alternativní e-mailovou adresu a získejte kód pro přístup k citlivým informacím.
    - Zadejte tento bezpečnostní kód.
    - Pokud chcete dvoustupňové ověřování nastavit později, vyberte Nastavit později.
    - Přejdete na stránku Správa aliasů účtu, kde se zobrazí vaše aliasy účtu. Pečlivě zkontrolujte, že se k portálu Azure EA přihlašujete pomocí primárního aliasu. Pokud se přihlašujete pomocí jiného aliasu, můžete ho nastavit jako svůj primární nebo místo něj použijte primární alias pro portál EA.

Pokud výše uvedený postup při řešení potíží selže, na stránce [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) odešlete žádost obsahující například následující informace:
- Použité prohlížeče a jejich verze
- Snímek obrazovky s chybovou zprávou
- Adresa URL stránky, na které se zobrazuje chyba  
- Datum, čas a časové pásmo výskytu chyby
- Kromě toho nám pomůže, když získáte soubor protokolu. Tady je postup zachycení trasování sítě:
  1. Otevřete Internet Explorer.
  1. Stiskněte klávesu F12. V dolní části aplikace Internet Explorer se otevře okno.
  1. Vyberte kartu **Síť**.
  1. Klikněte na **Spustit zachytávání**.
  1. Proveďte akci, která způsobuje chybu.
  1. Jakmile dojde k chybě, klikněte na **Zastavit zachytávání**.
  1. Uložte soubor se získanými informacemi a přiložte ho k žádosti o podporu.
  1. Nezapomeňte v žádosti o podporu uvést číslo vaší registrace a vaši e-mailovou adresu.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Jaký je rozdíl mezi pracovním nebo školním účtem a účtem Microsoft?

**Účet Microsoft:** Účty přidružené k účtu Live ID na stránce [https://signup.live.com](https://signup.live.com).

**Pracovní nebo školní účet:** K dispozici pouze pro společnosti, které si nastavily službu Active Directory s federací na cloud a všechny účty mají v jednom tenantovi. Pokud je interní služba Active Directory dané společnosti federovaná na cloud, je možné přidávat uživatele s ověřováním prostřednictvím jejich pracovních nebo školních účtů.

  Od září 2016 Microsoft již neumožňuje registraci pracovních nebo školních e-mailových adres jako účtů Microsoft. Další podrobnosti najdete v následujících materiálech: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Pokud vaše organizace není federovaná na cloud, nebudete moct použít svou pracovní nebo školní e-mailovou adresu. Místo toho si zaregistrujte nebo vytvořte novou e-mailovou adresu a zaregistrujte ji jako účet Microsoft.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Nemohu si vzpomenout na heslo k portálu Azure EA

Pokud jste zapomněli heslo svého účtu Microsoft Live ID, resetujte si ho na stránce [https://account.live.com/password/reset](https://account.live.com/password/reset).

Pokud jste zapomněli své pracovní heslo, obraťte se na správce IT vaší společnosti.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Mám platný pracovní nebo školní účet, ale nemůžu ho přidat na portál EA

Pokud máte pracovní nebo školní účet v jiném tenantovi, na stránce s podrobnostmi registrace změňte úroveň autorizace na Pracovní nebo školní účet napříč tenanty a budete moct účet přidat.

## <a name="next-steps"></a>Další kroky

- Správci na portálu Azure EA by si měli přečíst téma [Správa portálu Azure EA](ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
