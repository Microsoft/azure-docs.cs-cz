---
title: Vytvoření účtu Microsoft Developer | Azure Marketplace
description: Požadavky a kroky pro vytvoření účtu Microsoft Developer.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 522caa53123ad5ccb8076839d2dfa7dc1236b022
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818431"
---
<a name="create-a-microsoft-developer-account"></a>Vytvoření účtu Microsoft Developer
====================================

Tento článek popisuje, jak se stát schválenou vývojářem Microsoftu pro Azure Marketplace publikování.

## <a name="create-a-microsoft-account"></a>Vytvoření účtu Microsoft

Chcete-li spustit proces publikování, je nutné dokončit registraci **centra pro vývojáře od Microsoftu** . K zahájení procesu publikování použijete stejný registrovaný účet na **[portál partnerů cloudu](https://cloudpartner.azure.com/)** .

### <a name="general-account-guidelines"></a>Obecné pokyny k účtu

Pro nabídky Azure Marketplace doporučujeme mít jenom jednu účet Microsoft. Tento účet by neměl být specifický pro služby nebo nabídky.

Adresa, která tvoří uživatelské jméno, by měla být ve vaší doméně a řízena vaším IT týmem. Všechny související aktivity publikování by se měly provádět prostřednictvím tohoto účtu.

>[!WARNING]
>Slova jako "Azure" a "Microsoft" se pro registraci účet Microsoft nepodporují. Nepoužívejte tato slova k dokončení procesu vytváření a registrace účtu.

### <a name="company-account-guidelines"></a>Pokyny pro účet společnosti

Pokud bude k účtu mít přístup více než jedna osoba, přihlaste se pomocí účet Microsoft, který účet otevřel, podle těchto pokynů.

>[!IMPORTANT]
>Pokud chcete, aby přístup k vašemu účtu centra vývojářů měl více uživatelů, doporučujeme použít Azure Active Directory k přiřazení rolí jednotlivým uživatelům. Můžou získat přístup k účtu přihlášením pomocí svých individuálních přihlašovacích údajů služby Azure AD. Další informace najdete v tématu [Správa uživatelů účtu](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Účet Microsoft můžete vytvořit pomocí e-mailové adresy, která patří do domény\'s vaší společnosti, ale ne do jediného jednotlivce. Například WindowsApps\@fabrikam.com.
-   Omezte přístup k tomuto účet Microsoft na nejmenší možný počet vývojářů.
-   Nastavte distribuční seznam podnikových e-mailů, který zahrnuje všechny uživatele, kteří potřebují přístup k účtu vývojáře, a přidejte tuto e-mailovou adresu k bezpečnostním údajům. To umožňuje všem zaměstnancům v seznamu získávat v případě potřeby kódy zabezpečení a spravovat informace o zabezpečení účet Microsoft. Pokud není možné nastavit distribuční seznam, musí být vlastník jednotlivého e-mailového účtu dostupný pro přístup k bezpečnostnímu kódu, který se zobrazí po zobrazení výzvy (například když se do účtu přidá nové informace o zabezpečení nebo když musí být přístupný z nového zařízení).
-   Přidejte telefonní číslo společnosti, které nevyžaduje rozšíření a je přístupné pro klíčové členy týmu.
-   Obecně platí, že vývojáři používají důvěryhodná zařízení pro přihlášení k účtu vývojáře vaší společnosti. Všichni členové každého klíčového týmu by měli mít přístup k těmto důvěryhodným zařízením. Tím se sníží nutnost odeslání kódů zabezpečení při přístupu k účtu.
-   Pokud potřebujete povolený přístup k účtu z nedůvěryhodného počítače, omezte přístup maximálně na pět vývojářů. V ideálním případě mají tito vývojáři přístup k účtu z počítačů, které sdílejí stejné geografické umístění a umístění v síti.
-   Pravidelně kontrolujte [bezpečnostní údaje vaší společnosti](https://account.live.com/proofs/Manage) , abyste se ujistili, že jsou aktuální.

>[!IMPORTANT]
>K vašemu účtu vývojáře by měl být přistup primárně z důvěryhodných počítačů. To je důležité, protože existuje omezení počtu generovaných kódů na účet, za týden. Umožňuje taky bezproblémové přihlašování.
>
>Další informace najdete v tématech [Další pokyny k vývojářským účtům a zabezpečení](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Vytvoření účet Microsoft

1.  Otevřete novou relaci prohlížeče Chrome anonymním nebo Internet Explorer, abyste se ujistili, že nejste přihlášeni k existujícímu účtu.
2.  Zaregistrujte e-mail (pomocí předchozích pokynů) jako účet Microsoft pomocí tohoto [odkazu](https://signup.live.com/signup.aspx). Dokončete následující pokyny pro registraci:

    - Když zaregistrujete účet jako účet Microsoft, je nutné zadat platné telefonní číslo, které systému pošle ověřovací kód účtu jako textovou zprávu nebo automatizované volání.
    - Při registraci účtu jako účet Microsoft musíte zadat platné ID e-mailu pro příjem automatizovaného e-mailu pro ověření účtu.
    - Ověřte e-mailovou adresu odeslanou na adresu DL.

    Nyní jste připraveni použít nový účet Microsoft v centru pro vývojáře Microsoftu.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrace účtu na webu Microsoft Developer Center

K registraci firemních informací se používá Microsoft Developer Center. Základě musí být platným zástupcem společnosti a musí poskytovat své osobní údaje jako způsob ověření identity. Registrace osoby musí používat účet Microsoft sdílené pro společnost **a v portál partnerů cloudu se musí použít stejný účet.** Před pokusem o vytvoření účtu byste měli zkontrolovat, že vaše společnost ještě nemá účet Microsoft Developer Center. Během procesu budeme shromažďovat informace o adrese společnosti, informace o bankovním účtu a daňové informace. Ty se obvykle dají získat od finančních nebo podnikových kontaktů.

>[!IMPORTANT]
>Aby bylo možné postupovat podle různých fází vytváření a nasazování nabídek, je nutné dokončit následující komponenty profilu pro vývojáře.

| Vývojářský profil     | Spuštění konceptu    | Staging       | Publikovat bezplatnou šablonu a šablonu řešení   | Publikování komerčních   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Registrace společnosti  | Musí mít         | Musí mít     | Musí mít                             | Musí mít             |
| ID daňového profilu        | Nepovinné          | Nepovinné      | Nepovinné                              | Musí mít             |
| Bankovní účet          | Nepovinné          | Nepovinné      | Nepovinné                              | Musí mít             |

>[!NOTE]
>Používání vlastní licence (BYOL) je podporované jenom pro virtuální počítače a považuje se za bezplatnou nabídku.

### <a name="register-your-company-account"></a>Registrace účtu společnosti

1. Otevřete novou relaci procházení anonymním v Internet Exploreru nebo Chrome, abyste se ujistili, že nejste přihlášeni k osobnímu účtu.

2. Na webu [Windows Dev Center](https://dev.windows.com/registration?accountprogram=azure) se zaregistrujete jako prodávající. Než budete pokračovat, přečtěte si prosím následující důležitou poznámku.

   ![Ověřování účet Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Ujistěte se, že ID e-mailu nebo distribuční seznam (distribuční seznam se doporučuje odebrat závislost od jednotlivců), který budete používat k registraci v centru vývojářů, jako je první registrace jako účet Microsoft. Pokud ne, zaregistrujte ho pomocí tohoto odkazu. Pro registraci centra pro vývojáře se taky nedá použít jakékoli ID e-mailu v rámci domény společnosti Microsoft.

   ![Přihlášení ke vývojářskému centru](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Spusťte Průvodce "pomáhat nám chránit váš účet" a ověřte vaši identitu pomocí telefonního čísla nebo e-mailové adresy.

4. V části registrace – informace o účtu vyberte v rozevíracím seznamu **zemi nebo oblast účtu** a pak vyberte **Další**.

   ![Vybrat zemi/oblast](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Prodej od" zemí/oblastí: aby bylo možné prodávat vaše služby na Azure Marketplace, musí být registrovaná entita z jedné ze schválených zemí nebo oblastí "prodej od", které jsou uvedeny v rozevíracím seznamu. Toto omezení je pro výběr a daňové důvody. Další informace najdete v tématu Zásady pro účast na webu Marketplace.

5. Jako typ účtu vyberte **Společnost** a pak vyberte **Další**.

    >[!IMPORTANT]
    >Pro lepší pochopení typů účtů a rozhodování, který typ je pro vás nejvhodnější, zobrazte si typy účtů stránek, umístění a poplatky zobrazené v dalším snímku obrazovky.

    ![Typy účtů pro prodejce](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Zadejte **Zobrazovaný název vydavatele**. Obvykle se jedná o název vaší společnosti.

    >[!NOTE]
    >Zobrazované jméno vydavatele zadané v centru vývojářů se v Azure Marketplace nezobrazí po uvedení vaší nabídky. Tyto informace jsou však potřeba k dokončení procesu registrace.

7. Zadejte **kontaktní údaje** pro ověření účtu.

    >[!IMPORTANT]
    >Je nutné zadat přesné kontaktní informace, protože budou použity v rámci procesu ověřování, aby vaše společnost byla schválena v centru pro vývojáře. '

8. Zadejte kontaktní informace pro **schvalovatele společnosti**. Schvalovatel společnosti je osoba, která může ověřit, že máte oprávnění k vytvoření účtu v centru pro vývojáře jménem vaší organizace. Po zadání těchto informací vyberte **Další** a přejděte k **části platba**.

    ![Identifikace schvalovatele společnosti](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Zadejte platební údaje pro svůj účet. Pokud máte propagační kód, který pokrývá náklady na registraci, můžete sem zadat. V opačném případě zadejte informace o kreditních kartách (nebo PayPal na podporovaných trzích). Kliknutím na tlačítko **Další** přejdete k závěrečné **revizi**.

   ![Registrace platby](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Zkontrolujte informace o účtu a potvrďte, že vše je správné. Přečtěte si a přijměte podmínky a ujednání [smlouvy Microsoft Azure Marketplace vydavatele](https://go.microsoft.com/fwlink/?LinkID=699560). Zaškrtněte políčko, pokud chcete označit, že jste si přečetli a přijali tyto výrazy.

11. Kliknutím na **Dokončit** Potvrďte registraci. Na vaši e-mailovou adresu se pošle potvrzovací zpráva.

12. Pokud máte v úmyslu publikovat jenom bezplatné nabídky, vyberte [Přejít na portál partnerů cloudu](https://cloudpartner.azure.com/) a přejděte do části registrace účtu na portálu pro partnery cloudu v tomto článku.

### <a name="commercial-offers"></a>Komerční nabídky

Pokud plánujete publikovat komerční nabídky, jako je například nabídka virtuálních počítačů s využitím hodinového modelu fakturace, je nutné zadat daňové a bankovní informace. Přihlaste se k účtu centra pro vývojáře a vyberte **aktualizovat informace o účtu**. Postupujte podle pokynů v následující části "přidat bankovní a daňové informace".

>[!IMPORTANT]
>Nebudete moct doručovat komerční nabídku do produkčního prostředí bez poskytování informací o bankovním účtu a dani.

Pokud budete chtít později aktualizovat banku a daňové informace, můžete přejít na "zaregistrovat svůj účet na portálu pro partnery cloudu" v tomto článku.

>[!NOTE]
>Informace o bankovním účtu a dani doporučujeme co nejdříve poskytnout, protože ověřují daňové informace.

### <a name="add-banking-and-tax-information"></a>Přidat bankovní a daňové informace

Pokud chcete publikovat komerční nabídky k nákupu, musíte přidat výběr a daňové údaje a odeslat je k ověření v centru pro vývojáře.

**Poskytnutí informací o bance**

1.  Přihlaste se k [centru pro vývojáře Microsoftu](https://dev.windows.com/registration?accountprogram=azure) pomocí účet Microsoft.
2.  V nabídce vlevo vyberte položku **Výběr účtu** , v části **zvolit způsob platby**vyberte **bankovní účet** nebo **PayPal**.

    >[!NOTE]
    >Pokud máte komerční nabídky, které si zákazníci kupují na webu Marketplace, jedná se o účet, na který budete dostávat výběry těchto nákupů.
3.  Zadejte platební údaje a pak vyberte **Uložit**.

    >[!IMPORTANT]
    >Pokud potřebujete aktualizovat nebo změnit svůj účet pro výběr, postupujte podle předchozích kroků a nahraďte aktuální informace novými informacemi.
    >
    >Změna účtu výběr může zpozdit vaše platby až do jednoho platebního cyklu. K tomuto zpoždění dochází, protože potřebujeme ověřit změnu účtu, stejně jako při prvním nastavení účtu výběr. Po ověření účtu se pořád bude platit celá částka. jakékoli platby splatné za aktuální platební cyklus budou přidány do dalšího.

4.  Vyberte **Next** (Další).

**Poskytnutí informací o dani**

1.  Přihlaste se k [centru pro vývojáře Microsoftu](https://dev.windows.com/registration?accountprogram=azure) pomocí účet Microsoft (v případě potřeby).
2.  V nabídce vlevo vyberte možnost **daňový profil**.
3.  Na stránce **Nastavení daňového formuláře** :
    - Vyberte zemi nebo oblast, kde máte trvalé umístění.
    - Vyberte zemi nebo oblast, ve které jste si podržíte primární občanství.
    - Vyberte **Next** (Další).
4.  Zadejte podrobnosti o dani a pak vyberte **Další**.

>[!WARNING]
>Do produkčního prostředí nebudete moct doručovat své komerční nabídky, aniž byste v účtu Microsoft Developer Center poskytovali informace o bankovním účtu a dani.

### <a name="developer-center-registration-issues"></a>Problémy s registrací centra pro vývojáře

Pokud máte problémy s registrací centra pro vývojáře, použijte následující postup k otevření lístku podpory.

1.  Přejít na [odkaz podpory](https://developer.microsoft.com/windows/support).
2.  V části **kontaktujte nás**vyberte **Odeslat incident**.
    ![otevření lístku](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  Jako **typ problému**vyberte nápovědu pro Dev Center a pro **kategorii**vyberte publikovat a spravovat aplikace. Vyberte možnost **spustit e-mail**.

    ![identifikovat typ problému](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Bude vám udělena přihlašovací stránka. Přihlaste se pomocí libovolného účet Microsoft. Pokud nemáte účet Microsoft pak [ho vytvořte](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Zadejte podrobné informace o problému a vyberte **Odeslat** pro odeslání lístku.

    ![Odeslat lístek](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrace účtu na portálu pro partnery cloudu

Pomocí [portál partnerů cloudu](https://cloudpartner.azure.com/) můžete publikovat a spravovat vaše nabídky (y).

1.  Otevřete novou relaci prohlížeče Chrome anonymním nebo Internet Explorer, abyste se ujistili, že nejste přihlášeni k osobnímu účtu.
2.  Přejít na [portál partnerů cloudu](https://cloudpartner.azure.com/).
3.  Pokud jste nový uživatel a přihlásíte se k [portál partnerů cloudu](https://cloudpartner.azure.com/) poprvé, musíte se přihlásit pomocí stejného ID e-mailu, které je zaregistrované ve vašem účtu centra vývojářů. Tím se zajistí, že váš účet služby Dev Center a účet portálu Cloud Partner jsou vzájemně propojené.

Později můžete přidat další členy společnosti, kteří pracují na aplikaci. Podle postupu v následující části můžete jako přispěvatele nebo vlastníky na portálu partnerů cloudu.

Pokud jste na portálu Cloud Partner Portal přidali jako přispěvatele nebo vlastníka, můžete se přihlásit pomocí vlastního účtu.

>[!TIP]
>Zásady pro účast jsou popsané na webu Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Správa uživatelů jako vlastníků nebo přispěvatelů na portálu pro partnery cloudu

[Postup pro správu uživatelů na portálu partnerů cloudu](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Další kroky

Teď, když je váš účet vytvořený a zaregistrovaný, můžete spustit proces publikování na Azure Marketplace.
