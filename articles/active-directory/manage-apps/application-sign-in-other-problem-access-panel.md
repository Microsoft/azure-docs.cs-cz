---
title: Řešení potíží s přihlášením k aplikaci z Azure AD moje aplikace
description: Řešení potíží s přihlášením k aplikaci z Azure AD moje aplikace
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: 14b49c531ae11c056f9b6970e1ac00adcd68b296
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937164"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Řešení potíží s přihlášením k aplikaci z Azure AD moje aplikace

Moje aplikace je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazovat a spouštět cloudové aplikace, ke kterým správce Azure AD udělil přístup. K mým aplikacím se dostanete pomocí webového prohlížeče na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Další informace o používání služby Azure AD jako zprostředkovatele identity pro aplikaci najdete v tématu [co je Správa aplikací ve službě Azure AD](what-is-application-management.md). Pokud chcete rychle začít, podívejte se na [řadu rychlý Start při správě aplikací](view-applications-portal.md).

Tyto aplikace jsou nakonfigurované jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurována a přiřazena uživateli nebo skupině, pro kterou je uživatel členem, aby zobrazila aplikaci v mých aplikacích. 

Typ aplikací, které může uživatel zobrazit, spadají do následujících kategorií:
-   Microsoft 365 aplikací
-   Aplikace Microsoftu a aplikace třetích stran nakonfigurované pomocí jednotného přihlašování založeného na federaci
-   Aplikace jednotného přihlašování založené na heslech
-   Aplikace s existujícími řešeními jednotného přihlašování

Tady je několik věcí, které kontrolují, jestli se aplikace zobrazuje nebo nezobrazuje.
- Ujistěte se, že je aplikace přidaná do Azure AD, a ujistěte se, že je uživatel přiřazený. Další informace najdete v části [rychlý Start při správě aplikací](add-application-portal.md).
- Pokud se aplikace přidala v nedávné době, odhlaste se a znovu zaregistrujte. 
- Pokud aplikace vyžaduje licenci, jako je třeba Office, pak se ujistěte, že má uživatel přiřazenou příslušnou licenci.
- Doba potřebná pro změny licencování se může lišit v závislosti na velikosti a složitosti skupiny.

## <a name="general-issues-to-check-first"></a>Obecné problémy k první kontrole

-   Ujistěte se, že webový prohlížeč splňuje požadavky, v části [Moje aplikace podporuje prohlížeče](../user-help/my-apps-portal-end-user-access.md).
-   Ujistěte se, že prohlížeč uživatele přidal adresu URL aplikace do jejích **důvěryhodných webů**.
-   Přesvědčte se, zda je aplikace správně **nakonfigurována** .
-   Ujistěte se, že je **povolený** účet uživatele pro přihlášení.
-   Ujistěte se, že účet uživatele není **uzamčený.**
-   Ujistěte se, že **heslo uživatele není prošlé nebo zapomenuté.**
-   Ujistěte se, že **Multi-Factor Authentication** neblokuje přístup uživatelů.
-   Zajistěte, aby **zásady podmíněného přístupu** nebo zásady **ochrany identit** neblokovaly přístup uživatelů.
-   Ujistěte se, že **kontaktní údaje pro ověření** uživatele jsou aktuální, aby bylo možné vyhovět Multi-Factor Authentication nebo zásadám podmíněného přístupu.
-   Nezapomeňte taky vymazat soubory cookie v prohlížeči a zkusit se znovu přihlásit.

## <a name="problems-with-the-users-account"></a>Problémy s uživatelským účtem
Přístup k mým aplikacím může být blokovaný kvůli problému s uživatelským účtem. Níže jsou uvedeny některé způsoby, jak můžete řešit problémy s uživateli a jejich nastavení účtu:
-   [Ověřte, zda uživatelský účet existuje v Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Zkontroluje stav účtu uživatele.](#check-a-users-account-status)
-   [Resetování hesla uživatele](#reset-a-users-password)
-   [Povolení samoobslužného resetování hesel](#enable-self-service-password-reset)
-   [Zkontroluje stav vícefaktorového ověřování uživatele.](#check-a-users-multi-factor-authentication-status)
-   [Ověřit kontaktní údaje pro ověření uživatele](#check-a-users-authentication-contact-info)
-   [Ověřit členství uživatele ve skupinách](#check-a-users-group-memberships)
-   [Zjistit, jestli má uživatel více než 999 přiřazení role aplikace](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [Ověření licencí přiřazených uživateli](#check-a-users-assigned-licenses)
-   [Přiřazení licence uživateli](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Ověřte, zda uživatelský účet existuje v Azure Active Directory
Pokud chcete zjistit, jestli je uživatelský účet přítomen, postupujte takto:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Všichni uživatelé**.
6.  **Vyhledejte** uživatele, kterého vás zajímá, a **Vyberte řádek** , který chcete vybrat.
7.  Zkontrolujte vlastnosti objektu uživatele, abyste měli jistotu, že vypadají podle očekávání a že nechybí žádná data.

### <a name="check-a-users-account-status"></a>Zkontroluje stav účtu uživatele.
Chcete-li zjistit stav účtu uživatele, postupujte podle následujících kroků:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Všichni uživatelé**.
6.  **Vyhledejte** uživatele, kterého vás zajímá, a **Vyberte řádek** , který chcete vybrat.
7.  Vyberte **profil**.
8.  V části **Nastavení** zajistěte, aby se **blokování přihlášení** nastavilo na **ne**.

### <a name="reset-a-users-password"></a>Resetování hesla uživatele
Pokud chcete resetovat heslo uživatele, postupujte podle těchto kroků:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Všichni uživatelé**.
6.  **Vyhledejte** uživatele, kterého vás zajímá, a **Vyberte řádek** , který chcete vybrat.
7.  V horní části podokna uživatele vyberte tlačítko **resetovat heslo** .
8.  V zobrazeném podokně pro **resetování hesla** vyberte tlačítko **resetovat heslo** .
9.  Zkopírujte **dočasné heslo** nebo **Zadejte nové heslo** pro uživatele.
10. Sdělte tomuto uživateli nové heslo. při příštím přihlášení do Azure Active Directory je nutné toto heslo změnit.

### <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel
Pokud chcete povolit samoobslužné resetování hesla, postupujte podle těchto kroků nasazení:
-   [Povolit uživatelům resetovat hesla Azure Active Directory](../authentication/tutorial-enable-sspr.md)
-   [Povolit uživatelům resetovat nebo změnit místní hesla služby Active Directory](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Zkontroluje stav vícefaktorového ověřování uživatele.
Chcete-li zjistit stav vícefaktorového ověřování uživatele, postupujte takto:
1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2. Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4. V navigační nabídce vyberte **Uživatelé a skupiny** .
5. Vyberte **Všichni uživatelé**.
6. V horní části podokna vyberte tlačítko **Multi-Factor Authentication** .
7. Po načtení **portálu pro správu Multi-Factor Authentication** se ujistěte, že jste na kartě **Uživatelé** .
8. Vyhledejte uživatele v seznamu uživatelů hledáním, filtrováním nebo řazením.
9. Vyberte uživatele ze seznamu uživatelů a podle potřeby povolte, **zakažte** **nebo** **vypněte** službu Multi-Factor Authentication.
   >[!NOTE]
   >Pokud je uživatel v **vykonatelném** stavu, můžete ho nastavit tak, aby se dočasně **zakázal** , aby se mohl vrátit ke svému účtu. Až budou zpátky, můžete změnit jejich stav na **povoleno** , aby bylo možné znovu zaregistrovat své kontaktní údaje při příštím přihlášení. Případně můžete postupovat podle pokynů v [kontaktních informacích k ověření uživatele](#check-a-users-authentication-contact-info) a ověřit nebo nastavit tato data.

### <a name="check-a-users-authentication-contact-info"></a>Ověřit kontaktní údaje pro ověření uživatele
Pokud chcete zjistit kontaktní údaje pro ověření uživatele, které se používají pro službu Multi-Factor Authentication, podmíněný přístup, ochranu identity a resetování hesel, postupujte podle těchto kroků:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Všichni uživatelé**.
6.  **Vyhledejte** uživatele, kterého vás zajímá, a **Vyberte řádek** , který chcete vybrat.
7.  Vyberte **profil**.
8.  Přejděte dolů na **kontaktní údaje pro ověření**.
9.  **Zkontrolujte** data zaregistrovaná pro uživatele a podle potřeby je aktualizujte.

### <a name="check-a-users-group-memberships"></a>Ověřit členství uživatele ve skupinách
Pokud chcete ověřit členství uživatele ve skupině, postupujte podle těchto kroků:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Všichni uživatelé**.
6.  **Vyhledejte** uživatele, kterého vás zajímá, a **Vyberte řádek** , který chcete vybrat.
7.  Vyberte **skupiny** , chcete-li zobrazit skupiny, kterých je uživatel členem.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Zjistit, jestli má uživatel více než 999 přiřazení role aplikace
Pokud má uživatel přiřazeno více než 999 přiřazení rolí aplikace, pak nemusí zobrazit všechny své aplikace v mých aplikacích.

Důvodem je to, že moje aplikace teď načtou až 999 přiřazení role aplikace a určí aplikace, ke kterým se uživatelé přiřazují. Pokud je uživatel přiřazený k více než 999 aplikacím, není možné řídit, které z těchto aplikací se budou zobrazovat na portálu moje aplikace.

Pokud chcete zjistit počet přiřazení rolí aplikace udělených uživateli, postupujte podle těchto kroků:
1. Nainstalujte modul [**Microsoft. Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) PowerShell.
2. Spusťte `Connect-MgGraph -Scopes "Directory.Read.All"` a ověřte jako **globální správce.**
3. Spuštěním nástroje `$m = Get-MgUserAppRoleAssignment -UserId "<userId>" | Measure; $m.Count` určíte počet přiřazení rolí aplikace, které uživatel aktuálně udělil.

### <a name="check-a-users-assigned-licenses"></a>Ověření licencí přiřazených uživateli
Pokud chcete ověřit přiřazené licence uživatele, postupujte podle těchto kroků:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Všichni uživatelé**.
6.  **Vyhledejte** uživatele, kterého vás zajímá, a **Vyberte řádek** , který chcete vybrat.
7.  Vyberte **licence** , abyste viděli, které licence uživatel aktuálně přiřadil.

### <a name="assign-a-user-a-license"></a>Přiřazení licence uživateli 
K přiřazení licence uživateli použijte následující postup:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Všichni uživatelé**.
6.  **Vyhledejte** uživatele, kterého vás zajímá, a **Vyberte řádek** , který chcete vybrat.
7.  Vyberte **licence** , abyste viděli, které licence uživatel aktuálně přiřadil.
8.  Klikněte na tlačítko **přiřadit** .
9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.
10. **Volitelné** vyberte položku **možnosti přiřazení** pro přesnější přiřazení produktů. Vyberte **OK**.
11. Pro přiřazení těchto licencí tomuto uživateli vyberte tlačítko **přiřadit** .

## <a name="troubleshooting-deep-links"></a>Odstraňování potíží s přímými odkazy
Přímé odkazy nebo adresy URL pro přístup uživatelů jsou odkazy, které uživatelé můžou použít pro přístup k svým aplikacím jednotného přihlašování k heslům přímo z jejich panelů adresa URL prohlížeče. Když přejdete na tento odkaz, uživatelé se do aplikace automaticky přihlásí, aniž by museli nejprve přejít na moje aplikace. Odkaz je stejný jako ten, který uživatelé používají pro přístup k těmto aplikacím ze Spouštěče aplikací Microsoft 365.

### <a name="checking-the-deep-link"></a>Kontroluje se přímý odkaz.

Pokud chcete zjistit, jestli máte správný přímý odkaz, postupujte podle těchto kroků:
1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**
2. Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4. V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace**.
5. Vyberte **Všechny aplikace**. Zobrazí se seznam všech vašich aplikací.
   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**
6. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**
7. Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
8. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
9. V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace**.
10. Vyberte **Všechny aplikace**. Zobrazí se seznam všech vašich aplikací.
    * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**
11. Vyberte aplikaci, pro kterou chcete vrátit přímý odkaz.
12. Vyhledejte **adresu URL přístupu uživatele**. Váš přímý odkaz by měl odpovídat této adrese URL.

## <a name="contact-support"></a>Kontaktování podpory
Otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:
-   ID chyby korelace
-   UPN (uživatelská e-mailová adresa)
-   TenantID
-   Typ prohlížeče
-   Časové pásmo a čas/časový rámec při výskytu chyby
-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
- [Série rychlý Start při správě aplikací](view-applications-portal.md)
