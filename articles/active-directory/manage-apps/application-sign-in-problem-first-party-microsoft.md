---
title: Problémy s přihlášením k aplikaci microsoftu | Dokumenty společnosti Microsoft
description: Řešení běžných problémů, kterým čelípři přihlašování k aplikacím Microsoft první strany pomocí Azure AD (jako je Office 365)
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee8802aeb2a760e255ab4f5e99010dfedc45e0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108304"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problémy s přihlášením k aplikaci Microsoft

Microsoft Aplikace (jako Office 365 Exchange, SharePoint, Yammer, atd.) jsou přiřazeny a spravovány trochu jinak než aplikace SaaS třetích stran nebo jiné aplikace, které integrujete s Azure AD pro jednotné přihlašování.

Existují tři hlavní způsoby, které uživatel může získat přístup k aplikaci publikované společností Microsoft.

-   U aplikací v Office 365 nebo jiných placených sadách je uživatelům udělen přístup prostřednictvím **přiřazení licence** buď přímo k jejich uživatelskému účtu, nebo prostřednictvím skupiny využívající možnosti přiřazení licencí založených na skupině.

-   U aplikací, které společnost Microsoft nebo třetí strana zveřejňuje volně pro každého, kdo je může používat, může být uživatelům udělen přístup prostřednictvím **souhlasu uživatele**. To znamená, že se přihlásí k aplikaci pomocí svého účtu Azure AD Work nebo School a umožní jí přístup k některé omezené sadě dat na svém účtu.

-   U aplikací, které společnost Microsoft nebo třetí strana publikuje volně pro každého, kdo může používat, může být uživatelům udělen přístup také na základě **souhlasu správce**. To znamená, že správce určil, že aplikace může být použita všemi v organizaci, takže se k aplikaci přihlásí pomocí účtu globálního správce a udělí přístup všem uživatelům v organizaci.

Chcete-li problém vyřešit, začněte s [obecnými problémovými oblastmi s přístupem k aplikacím, abyste zvážili](#general-problem-areas-with-application-access-to-consider) a přečetli si návod: Kroky k řešení potíží s přístupem k aplikacím společnosti Microsoft, abyste se dostali k podrobnostem.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Obecné problémové oblasti s přístupem k aplikacím, které je třeba zvážit

Následuje seznam obecných problémových oblastí, do kterých můžete přejít, pokud máte představu o tom, kde začít, ale doporučujeme přečíst si návod, abyste mohli rychle začít: Návod: Kroky k řešení potíží s přístupem k aplikacím Microsoft Application.

-   [Problémy s uživatelským účtem](#problems-with-the-users-account)

-   [Problémy se skupinami](#problems-with-groups)

-   [Problémy se zásadami podmíněného přístupu](#problems-with-conditional-access-policies)

-   [Problémy se souhlasem aplikace](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Postup řešení potíží s přístupem k aplikacím společnosti Microsoft

Níže jsou uvedeny některé běžné problémy lidé narazit, když jejich uživatelé nemohou přihlásit k aplikaci společnosti Microsoft.

- Obecné problémy, které je třeba nejprve zkontrolovat

  * Ujistěte se, že se uživatel přihlašuje ke **správné adrese URL** a ne k místní adrese URL aplikace.

  * Ujistěte se, že uživatelský účet **není uzamčen.**

  * Ujistěte se, **že účet uživatele existuje** ve službě Azure Active Directory. [Kontrola, zda ve službě Azure Active Directory existuje uživatelský účet](#problems-with-the-users-account)

  * Zkontrolujte, zda je pro přihlášení **povolen** uživatelský [účet.](#problems-with-the-users-account)

  * Ujistěte se, že vypršela platnost hesla uživatele nebo zda **není zapomenuto.** [Resetování hesla uživatele](#reset-a-users-password) nebo [povolení samoobslužného resetování hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Ujistěte se, že **vícefaktorové ověřování** neblokuje přístup uživatelů. [Kontrola stavu vícefaktorového ověřování uživatele](#check-a-users-multi-factor-authentication-status) nebo [kontrola kontaktních údajů uživatele](#check-a-users-authentication-contact-info) pro ověřování

  * Ujistěte se, že **zásady podmíněného přístupu** nebo **zásady ochrany identity** neblokují přístup uživatelů. [Zkontrolujte konkrétní zásady podmíněného přístupu](#problems-with-conditional-access-policies) nebo [zkontrolujte zásady podmíněného přístupu konkrétní aplikace](#check-a-specific-applications-conditional-access-policy) nebo [zakažte určitou zásadu podmíněného přístupu](#disable-a-specific-conditional-access-policy)

  * Ujistěte se, že jsou informace **o ověřovacím kontaktu** uživatele aktuální, aby bylo možné vynucovat zásady vícefaktorového ověřování nebo podmíněného přístupu. [Kontrola stavu vícefaktorového ověřování uživatele](#check-a-users-multi-factor-authentication-status) nebo [kontrola kontaktních údajů uživatele](#check-a-users-authentication-contact-info) pro ověřování

- U **aplikací Microsoftu, které vyžadují licenci** (například Office365), je třeba zkontrolovat několik konkrétních problémů, jakmile vyloučíte výše uvedené obecné problémy: **Microsoft**

  * Ujistěte se, že uživatel nebo má **přiřazenou licenci.** [Kontrola přiřazených licencí uživatele](#check-a-users-assigned-licenses) nebo [kontrola přiřazených licencí skupiny](#check-a-groups-assigned-licenses)

  * Pokud je licence **přiřazena** **ke statické skupině**, ujistěte se, že je uživatel **členem** této skupiny. [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

  * Pokud je licence **přiřazena** **dynamické skupině**, ujistěte se, že je pravidlo dynamické **skupiny správně nastaveno**. [Kontrola kritérií členství dynamické skupiny](#check-a-dynamic-groups-membership-criteria)

  * Pokud je licence **přiřazena** **dynamické skupině**, ujistěte se, že dynamická skupina **dokončila zpracování** svého členství a že uživatel je **členem** (to může nějakou dobu trvat). [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

  *  Jakmile se ujistíte, že je licence přiřazena, ujistěte se, že platnost licence **nevypršela**.

  *  Ujistěte se, že licence je **pro aplikaci,** ke které přistupují.

- U aplikací **společnosti Microsoft,** **které nevyžadují licenci**, je třeba zkontrolovat následující skutečnosti:

  * Pokud aplikace požaduje **oprávnění na úrovni uživatele** (například "Přístup k poštovní schránce tohoto uživatele"), ujistěte se, že se uživatel přihlásil k aplikaci a provedl **operaci souhlasu na úrovni uživatele,** aby aplikace získala přístup k jejich datům.

  * Pokud aplikace požaduje **oprávnění na úrovni správce** (například "Přístup k poštovním schránkám všech uživatelů"), ujistěte se, že globální správce provedl **operaci souhlasu na úrovni správce jménem všech uživatelů** v organizaci.

## <a name="problems-with-the-users-account"></a>Problémy s uživatelským účtem

Přístup k aplikaci lze blokovat kvůli problému s uživatelem, který je přiřazen k aplikaci. Níže jsou uvedeny některé způsoby, jak můžete řešit a řešit problémy s uživateli a jejich nastavení účtu:

-   [Kontrola, zda ve službě Azure Active Directory existuje uživatelský účet](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrola stavu účtu uživatele](#check-a-users-account-status)

-   [Resetování hesla uživatele](#reset-a-users-password)

-   [Povolení samoobslužného resetování hesla](#enable-self-service-password-reset)

-   [Kontrola stavu vícefaktorového ověřování uživatele](#check-a-users-multi-factor-authentication-status)

-   [Kontrola kontaktních údajů uživatele pro ověřování](#check-a-users-authentication-contact-info)

-   [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Kontrola přiřazených licencí uživatele](#check-a-users-assigned-licenses)

-   [Přiřazení licence uživateli](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrola, zda ve službě Azure Active Directory existuje uživatelský účet

Chcete-li zkontrolovat, zda je k dispozici uživatelský účet, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Zkontrolujte vlastnosti objektu uživatele a ujistěte se, že vypadají podle očekávání a chybí žádná data.

### <a name="check-a-users-account-status"></a>Kontrola stavu účtu uživatele

Chcete-li zkontrolovat stav účtu uživatele, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  klepněte na **položku Profil**.

8.  V části **Nastavení** zkontrolujte, zda je **přihlášení bloku** nastaveno na **ne**.

### <a name="reset-a-users-password"></a>Resetování hesla uživatele

Chcete-li obnovit heslo uživatele, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  klikněte na tlačítko **Obnovit heslo** v horní části podokna uživatele.

8.  Klepněte na tlačítko **Obnovit heslo** v podokně **Obnovit heslo,** které se zobrazí.

9.  Zkopírujte **dočasné heslo** nebo **zadejte nové heslo** pro uživatele.

10. Sdělte toto nové heslo uživateli, bude nutné změnit toto heslo během jejich další přihlášení do služby Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Chcete-li povolit samoobslužné resetování hesla, postupujte podle následujících kroků nasazení:

-   [Povolení uživatelům resetovat hesla služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Povolení uživatelům resetovat nebo změnit místní hesla služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrola stavu vícefaktorového ověřování uživatele

Chcete-li zkontrolovat stav vícefaktorového ověřování uživatele, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. klikněte na tlačítko **Vícefaktorové ověřování** v horní části podokna.

7. Jakmile se **portál správy vícefaktorového ověřování** načte, ujistěte se, že jste na kartě **Uživatelé.**

8. Vyhledejte uživatele v seznamu uživatelů vyhledáváním, filtrováním nebo řazením.

9. Vyberte uživatele ze seznamu uživatelů a **povolit**, **zakázat**nebo **vynutit** vícefaktorové ověřování podle potřeby.

   * **Poznámka:** Pokud je uživatel v **vynuceném** stavu, můžete jej dočasně nastavit na **Zakázáno,** aby se mohl vrátit do svého účtu. Jakmile jsou zpět, pak můžete změnit jejich stav **povoleno** znovu požadovat, aby znovu zaregistrovat své kontaktní údaje během jejich další přihlášení. Případně můžete podle kroků v části [Kontrola ověřovacích kontaktních údajů uživatele](#check-a-users-authentication-contact-info) ověřit nebo nastavit tato data pro ně.

### <a name="check-a-users-authentication-contact-info"></a>Kontrola kontaktních údajů uživatele pro ověřování

Chcete-li zkontrolovat kontaktní údaje uživatele pro ověřování pomocí vícefaktorového ověřování, podmíněného přístupu, ochrany identity a resetování hesla, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  klepněte na **položku Profil**.

8.  Posuňte se dolů na **Informace o autentizaci**.

9.  **Zkontrolujte** data registrovaná pro uživatele a podle potřeby aktualizujte.

### <a name="check-a-users-group-memberships"></a>Kontrola členství uživatele ve skupinách

Chcete-li zkontrolovat členství uživatele ve skupinách, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Kliknutím na **Skupiny** zobrazíte, ve kterých skupinách je uživatel členem.

### <a name="check-a-users-assigned-licenses"></a>Kontrola přiřazených licencí uživatele

Chcete-li zkontrolovat přiřazené licence uživatele, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Chcete-li zjistit, které licence uživatel aktuálně přiřadil, klepněte na **položku Licence.**

### <a name="assign-a-user-a-license"></a>Přiřazení licence uživateli 

Chcete-li uživateli přiřadit licenci, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Chcete-li zjistit, které licence uživatel aktuálně přiřadil, klepněte na **položku Licence.**

8.  klikněte na tlačítko **Přiřadit.**

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelně** klepněte na položku **možností přiřazení,** chcete-li granularly přiřadit produkty. Po dokončení klepněte na **tlačítko Ok.**

11. Klepnutím na tlačítko **Přiřadit** přiřadíte tyto licence tomuto uživateli.

## <a name="problems-with-groups"></a>Problémy se skupinami

Přístup k aplikaci lze blokovat kvůli problému se skupinou, která je přiřazena k aplikaci. Níže jsou uvedeny některé způsoby řešení a řešení problémů se skupinami a členstvím ve skupinách:

-   [Kontrola členství ve skupině](#check-a-groups-membership)

-   [Kontrola kritérií členství dynamické skupiny](#check-a-dynamic-groups-membership-criteria)

-   [Kontrola přiřazených licencí skupiny](#check-a-groups-assigned-licenses)

-   [Opětovné zpracování licencí skupiny](#reprocess-a-groups-licenses)

-   [Přiřazení skupiny k licenci](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Kontrola členství ve skupině

Chcete-li zkontrolovat členství ve skupině, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na **položku Všechny skupiny**.

6.  **Vyhledejte** skupinu, která vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Klepnutím na tlačítko **Členové** zkontrolujte seznam uživatelů přiřazených k této skupině.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Kontrola kritérií členství dynamické skupiny 

Chcete-li zkontrolovat kritéria členství dynamické skupiny, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na **položku Všechny skupiny**.

6.  **Vyhledejte** skupinu, která vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  klikněte na **Dynamická pravidla členství.**

8.  Zkontrolujte **jednoduché** nebo **rozšířené** pravidlo definované pro tuto skupinu a ujistěte se, že uživatel, který má být členem této skupiny, splňuje tato kritéria.

### <a name="check-a-groups-assigned-licenses"></a>Kontrola přiřazených licencí skupiny

Chcete-li zkontrolovat přiřazené licence skupiny, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na **položku Všechny skupiny**.

6.  **Vyhledejte** skupinu, která vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Chcete-li zjistit, které licence skupina aktuálně přiřadila, klepněte na **položku Licence.**

### <a name="reprocess-a-groups-licenses"></a>Opětovné zpracování licencí skupiny

Chcete-li znovu zpracovat přiřazené licence skupiny, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na **položku Všechny skupiny**.

6. **Vyhledejte** skupinu, která vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. Chcete-li zjistit, které licence skupina aktuálně přiřadila, klepněte na **položku Licence.**

8. Klepnutím na tlačítko **Znovu zpracovat** zajistíte, že licence přiřazené členům této skupiny jsou aktuální. To může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny.

   >[!NOTE]
   >Chcete-li to provést rychleji, zvažte dočasné přiřazení licence přímo uživateli. [Přiřazení licence uživateli](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Přiřazení skupiny k licenci

Chcete-li skupině přiřadit licenci, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na **položku Všechny skupiny**.

6. **Vyhledejte** skupinu, která vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. Chcete-li zjistit, které licence skupina aktuálně přiřadila, klepněte na **položku Licence.**

8. klikněte na tlačítko **Přiřadit.**

9. Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelně** klepněte na položku **možností přiřazení,** chcete-li granularly přiřadit produkty. Po dokončení klepněte na **tlačítko Ok.**

11. Klepnutím na tlačítko **Přiřadit** přiřadíte tyto licence této skupině. To může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny.

    >[!NOTE]
    >Chcete-li to provést rychleji, zvažte dočasné přiřazení licence přímo uživateli. [Přiřazení licence uživateli](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problémy se zásadami podmíněného přístupu

### <a name="check-a-specific-conditional-access-policy"></a>Kontrola konkrétní zásady podmíněného přístupu

Kontrola nebo ověření jedné zásady podmíněného přístupu:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Podnikové aplikace.**

5. klikněte na navigační položku **podmíněného přístupu.**

6. klikněte na zásady, které máte zájem o kontrolu.

7. Zkontrolujte, zda neexistují žádné konkrétní podmínky, přiřazení nebo jiná nastavení, která mohou blokovat přístup uživatelů.

   >[!NOTE]
   >Můžete chtít dočasně zakázat tyto zásady, abyste se ujistili, že neovlivňuje přihlášení. Chcete-li to provést, nastavte přepínač **Povolit zásady** na **Ne** a klepněte na tlačítko **Uložit.**
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Kontrola zásad podmíněného přístupu konkrétní aplikace

Kontrola nebo ověření aktuálně nakonfigurované zásady podmíněného přístupu jedné aplikace:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Podnikové aplikace.**

5.  klepněte na **položku Všechny aplikace**.

6.  Vyhledejte aplikaci, která vás zajímá, nebo se uživatel pokouší přihlásit pomocí zobrazované aplikace nebo ID aplikace.

     >[!NOTE]
     >Pokud hledáte aplikaci, klikněte na tlačítko **Filtr** a rozbalte rozsah seznamu na **Všechny aplikace**. Pokud chcete zobrazit další sloupce, klikněte na tlačítko **Sloupce** a přidejte další podrobnosti o svých aplikacích.
     >
     >

7.  klikněte na navigační položku **podmíněného přístupu.**

8.  klikněte na zásady, které máte zájem o kontrolu.

9.  Zkontrolujte, zda neexistují žádné konkrétní podmínky, přiřazení nebo jiná nastavení, která mohou blokovat přístup uživatelů.

     >[!NOTE]
     >Můžete chtít dočasně zakázat tyto zásady, abyste se ujistili, že neovlivňuje přihlášení. Chcete-li to provést, nastavte přepínač **Povolit zásady** na **Ne** a klepněte na tlačítko **Uložit.**
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Zakázání určité zásady podmíněného přístupu

Kontrola nebo ověření jedné zásady podmíněného přístupu:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Podnikové aplikace.**

5.  klikněte na navigační položku **podmíněného přístupu.**

6.  klikněte na zásady, které máte zájem o kontrolu.

7.  Zakažte zásadu nastavením **přepínače Povolit zásadu** na **Ne** a klepnutím na tlačítko **Uložit.**

## <a name="problems-with-application-consent"></a>Problémy se souhlasem aplikace

Přístup k aplikaci lze blokovat, protože nedošlo k operaci souhlasu se správnými oprávněními. Níže jsou uvedeny některé způsoby řešení a řešení problémů se souhlasem aplikace:

-   [Provedení operace souhlasu na úrovni uživatele](#perform-a-user-level-consent-operation)

-   [Provedení operace souhlasu na úrovni správce pro libovolnou aplikaci](#perform-administrator-level-consent-operation-for-any-application)

-   [Provedení souhlasu na úrovni správce pro aplikaci s jedním klientem](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Provedení souhlasu na úrovni správce pro víceklientské aplikace](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Provedení operace souhlasu na úrovni uživatele

-   U jakékoli aplikace s podporou open ID Connect, která požaduje oprávnění, provede přechod na přihlašovací obrazovku aplikace souhlas na úrovni uživatele s aplikací přihlášeného uživatele.

-   Chcete-li to provést programově, přečtěte si téma [Požadování souhlasu jednotlivých uživatelů](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Provedení operace souhlasu na úrovni správce pro libovolnou aplikaci

-   Pouze **u aplikací vyvinutých pomocí aplikačního modelu V1**můžete vynutit, aby k tomuto souhlasu na úrovni správce došlo přidáním "**\_?prompt=admin consent**" na konec přihlašovací adresy URL aplikace.

-   U **všech aplikací vyvinutých pomocí aplikačního modelu V2**můžete tento souhlas na úrovni správce vynutit podle pokynů v části **Požádat o oprávnění od** správce adresáře [v části Použití koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Provedení souhlasu na úrovni správce pro aplikaci s jedním klientem

-   U **jednoklientských aplikací,** které požadují oprávnění (například ty, které vyvíjíte nebo vlastníte ve vaší organizaci), můžete provést operaci **souhlasu** na úrovni správy jménem všech uživatelů tak, že se přihlásíte jako globální správce a kliknete na tlačítko **Udělit oprávnění** v horní části **registru aplikací –&gt; všechny aplikace –&gt; vyberte aplikaci –&gt; požadovaná oprávnění.**

-   U **všech aplikací vyvinutých pomocí aplikačního modelu V1 nebo V2**můžete tento souhlas na úrovni správce vynutit podle pokynů v části **Požádat o oprávnění od** správce adresáře [v části Použití koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Provedení souhlasu na úrovni správce pro víceklientské aplikace

-   U **víceklientských aplikací,** které požadují oprávnění (například aplikace, kterou vyvíjí třetí strana nebo společnost Microsoft), můžete provést operaci **souhlasu na úrovni správy.** Přihlaste se jako globální správce a klikněte na tlačítko **Udělit oprávnění** v části **Podnikové aplikace –&gt; Všechny aplikace –&gt; Vyberte aplikaci –&gt; podokno oprávnění** (brzy k dispozici).

-   Tento souhlas na úrovni správce můžete také vynutit podle pokynů v části **Požádat o oprávnění od** správce adresáře v části Použití [koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Další kroky
[Použití koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

