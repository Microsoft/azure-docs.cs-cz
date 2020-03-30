---
title: Vytvoření účtu Microsoft Developer | Azure Marketplace
description: Požadavky a postup pro vytvoření účtu Microsoft Developer.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280367"
---
<a name="create-a-microsoft-developer-account"></a>Vytvoření účtu Microsoft Developer
====================================

Tento článek popisuje, jak se stát schváleným publikováním microsoft developerpro Azure Marketplace.

## <a name="create-a-microsoft-account"></a>Vytvoření účtu Microsoft

Chcete-li zahájit proces publikování, budete muset dokončit registraci **centra pro vývojáře microsoftu.** Ke spuštění procesu publikování použijete stejný registrovaný účet na **[portálu cloudových partnerů.](https://cloudpartner.azure.com/)**

### <a name="general-account-guidelines"></a>Obecné pokyny pro účet

Doporučujeme, abyste pro své nabídky na Azure Marketplace měli jenom jeden účet Microsoft. Tento účet by neměl být specifický pro služby nebo nabídky.

Adresa, která tvoří uživatelské jméno, by měla být ve vaší doméně a řízena týmem IT. Všechny aktivity související s publikováním by měly být prováděny prostřednictvím tohoto účtu.

>[!WARNING]
>Slova jako "Azure" a "Microsoft" nejsou pro registraci účtu Microsoft podporována. Nepoužívejte tato slova k dokončení procesu vytváření a registrace účtu.

### <a name="company-account-guidelines"></a>Pokyny k účtu společnosti

Pokud se k účtu bude muset přihlásit více než jedna osoba, budete se přihlašovat pomocí účtu Microsoft, který účet otevřel, postupujte podle těchto pokynů.

>[!IMPORTANT]
>Chcete-li povolit přístup k vašemu účtu Dev Center více uživatelům, doporučujeme použít Azure Active Directory k přiřazení rolí jednotlivým uživatelům. Přístup k účtu mohou získat po přihlášení pomocí svých individuálních přihlašovacích údajů azure ad. Další informace naleznete v tématu [Správa uživatelů účtu](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Vytvořte si účet Microsoft pomocí e-mailové adresy, která patří do vaší firemní\'domény, ale ne pro jednu osobu. Například windowsapps\@fabrikam.com.
-   Omezte přístup k tomuto účtu Microsoft na nejmenší možný počet vývojářů.
-   Nastavte firemní seznam pro distribuci e-mailů, který bude shrávat všechny uživatele, kteří potřebují přístup k vývojářskému účtu, a přidejte tuto e-mailovou adresu do bezpečnostních údajů. To umožňuje všem zaměstnancům v seznamu přijímat bezpečnostní kódy v případě potřeby a spravovat bezpečnostní údaje vašeho účtu Microsoft. Pokud nastavení distribučního seznamu není možné, vlastník jednotlivého e-mailového účtu bude muset být k dispozici pro přístup a sdílení bezpečnostního kódu po zobrazení výzvy (například při přidání nových bezpečnostních údajů do účtu nebo při přístupu z nového zařízení.)
-   Přidejte telefonní číslo společnosti, které nevyžaduje rozšíření a je přístupné klíčovým členům týmu.
-   Obecně platí, že vývojáři používají důvěryhodná zařízení k přihlášení k vývojářskému účtu vaší společnosti. Všichni klíčoví členové týmu by měli mít přístup k těmto důvěryhodným zařízením. Tím se sníží potřeba bezpečnostních kódů, které mají být odeslány při přístupu k účtu.
-   Pokud potřebujete povolit přístup k účtu z nedůvěryhodného počítače, omezte tento přístup maximálně na pět vývojářů. V ideálním případě by tito vývojáři měli přistupovat k účtu z počítačů, které sdílejí stejné geografické a síťové umístění.
-   Často kontrolujte [bezpečnostní údaje vaší společnosti,](https://account.live.com/proofs/Manage) abyste se ujistili, že jsou aktuální.

>[!IMPORTANT]
>K vývojářskému účtu by měl být přístup náhlavním systému především z důvěryhodných počítačů. To je velmi důležité, protože počet kódů generovaných na účet za týden je omezen. Umožňuje také nejplynulejší přihlašovací prostředí.
>
>Další informace naleznete v [dalších pokynech k účtu pro vývojáře a zabezpečení](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Vytvoření účtu Microsoft

1.  Otevřete novou relaci procházení Chrome Incognito nebo Internet Explorer InPrivate, abyste zajistili, že nejste přihlášeni k existujícímu účtu.
2.  Zaregistrujte e-mail (podle předchozích pokynů) jako účet Microsoft pomocí tohoto [odkazu](https://signup.live.com/signup.aspx). Vyplňte následující pokyny k registraci:

    - Při registraci účtu jako účtu Microsoft musíte zadat platné telefonní číslo, na které vám systém pošle ověřovací kód účtu jako textovou zprávu nebo automatický hovor.
    - Při registraci účtu jako účtu Microsoft musíte zadat platné e-mailové id pro příjem automatického e-mailu pro ověření účtu.
    - Ověřte e-mailovou adresu odeslanou dl.

    Nyní jste připraveni použít nový účet Microsoft v Centru pro vývojáře Microsoft.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrace účtu v Microsoft Developer Center

Centrum pro vývojáře společnosti Microsoft se používá k registraci informací o společnosti jednou. Žadatel o registraci musí být platným zástupcem společnosti a musí poskytnout své osobní údaje jako způsob ověření jejich totožnosti. Osoba, která se registruje, musí používat účet Microsoft, který je pro společnost sdílený, **a stejný účet musí být použit na portálu partnerů cloudu.** Před pokusem o jeho vytvoření byste měli zkontrolovat, zda vaše společnost ještě nemá účet Microsoft Developer Center. Během procesu budeme shromažďovat informace o adrese společnosti, informace o bankovním účtu a daňové informace. Ty jsou obvykle dostupné z finančních nebo obchodních kontaktů.

>[!IMPORTANT]
>Chcete-li pokračovat v různých fázích vytváření a nasazování nabídek, je nutné provést následující součásti profilu vývojáře.

| Profil vývojáře     | Spuštění konceptu    | Příprava       | Publikování bezplatné šablony a řešení   | Publikovat komerční   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Registrace společnosti  | Musí mít         | Musí mít     | Musí mít                             | Musí mít             |
| ID daňového profilu        | Nepovinné          | Nepovinné      | Nepovinné                              | Musí mít             |
| Bankovní účet          | Nepovinné          | Nepovinné      | Nepovinné                              | Musí mít             |

>[!NOTE]
>Přineste si vlastní licenci (BYOL) je podporována pouze pro virtuální počítače a je považován za bezplatnou nabídku.

### <a name="register-your-company-account"></a>Registrace firemního účtu

1. Otevřete novou relaci procházení Aplikace Internet Explorer InPrivate nebo Chrome Incognito, abyste zajistili, že nejste přihlášeni k osobnímu účtu.

2. Přejděte do [centra pro střední prostředí Windows](https://dev.windows.com/registration?accountprogram=azure) a zaregistrujte se jako prodejce. Než budete pokračovat, přečtěte si následující důležitou poznámku.

   ![Ověření účtu Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Ujistěte se, že id e-mailu nebo distribuční seznam (distribuční seznam se doporučuje odebrat závislost od jednotlivců), které budete používat pro registraci v Centru pro dev je nejprve registrovánjako účet Microsoft. Pokud ne, zaregistrujte se pomocí tohoto odkazu. Žádné id e-mailu pod doménou společnosti Microsoft také nelze použít pro registraci centra Pro dev Center.

   ![Přihlášení do centra dev](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Spusťte průvodce "Pomozte nám chránit váš účet" a ověřte svou identitu pomocí telefonního čísla nebo e-mailové adresy.

4. V části Informace o registračním účtu vyberte v rozevíracím seznamu **zemi nebo oblast účtu** a pak vyberte **Další**.

   ![Vybrat zemi nebo oblast](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >Země nebo oblasti prodeje: Aby bylo možné prodávat své služby na Azure Marketplace, musí vaše registrovaná entita posuzovat jednu ze schválených zemí nebo oblastí prodeje z rozbalovacího seznamu. Toto omezení je z výplatních a daňových důvodů. Další informace najdete v tématu zásady účasti na Marketplace.

5. Jako typ účtu vyberte **Společnost** a pak vyberte **Další**.

    >[!IMPORTANT]
    >Chcete-li lépe porozumět typům účtů a rozhodnout, který typ je pro vás nejvhodnější, zobrazte stránku Typy účtů, místa a poplatky zobrazené v dalším zachycení obrazovky.

    ![Typy účtů pro prodejce](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Zadejte **zobrazovaný název aplikace Publisher**. Obvykle se jedná o název vaší společnosti.

    >[!NOTE]
    >Zobrazované jméno vydavatele zadané v Centru pro dev se po uvedení nabídky na Azure Marketplace nezobrazí na Webu Azure Marketplace. Ale tyto informace jsou potřebné k dokončení procesu registrace.

7. Zadejte **kontaktní údaje** pro ověření účtu.

    >[!IMPORTANT]
    >Musíte poskytnout přesné kontaktní údaje, protože budou použity v našem ověřovacím procesu, aby vaše společnost byla schválena v Centru pro vývojáře."

8. Zadejte kontaktní informace pro **schvalovatele společnosti**. Schvalovatel společnosti je osoba, která může ověřit, že jste oprávněni vytvořit účet v Centru pro spojené s používáním reklam jménem vaší organizace. Po zadání těchto informací vyberte **Další,** chcete-li přejít do **oddílu Platba**.

    ![Identifikovat schvalovatele společnosti](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Zadejte platební údaje svého účtu. Pokud máte promo kód, který pokrývá náklady na registraci, můžete zadat, že zde. V opačném případě uveďte informace o své kreditní kartě (nebo PayPal na podporovaných trzích). Výběrem **možnosti Další** přejděte na závěrečnou **recenzi**.

   ![Registrace platby](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Zkontrolujte informace o svém účtu a zkontrolujte, zda je vše v pořádku. Přečtěte si a přijměte podmínky [smlouvy s vydavatelem webu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Zaškrtnutím políčka označíte, že jste si tyto podmínky přečetli a přijali je.

11. Chcete-li registraci potvrdit, vyberte **možnost Dokončit.** Na vaši e-mailovou adresu je odeslána potvrzovací zpráva.

12. Pokud plánujete publikovat jenom bezplatné nabídky, vyberte [Přejít na portál partnerů cloudu](https://cloudpartner.azure.com/) a v tomto článku přeskočíte na "Zaregistrovat svůj účet na portálu cloudových partnerů".

### <a name="commercial-offers"></a>Komerční nabídky

Pokud plánujete publikovat komerční nabídky, jako je například nabídka virtuálního počítače pomocí modelu hodinové fakturace, musíte poskytnout daňové a bankovní informace. Za tímto účelem se přihlaste ke svému účtu Centra pro vývojáře a vyberte **Aktualizovat informace o účtu**. Postupujte podle pokynů v další části "Přidat bankovní a daňové údaje".

>[!IMPORTANT]
>Nebudete moci tlačit obchodní nabídku do výroby bez poskytnutí bankovního účtu a daňových informací.

Pokud dáváte přednost pozdější aktualizaci bankovních a daňových údajů, můžete v tomto článku přeskočit na "Zaregistrovat svůj účet na portálu cloudových partnerů".

>[!NOTE]
>Doporučujeme poskytnout bankovní účet a daňové údaje co nejdříve, protože ověření daňových údajů nějakou dobu trvá.

### <a name="add-banking-and-tax-information"></a>Přidání bankovních a daňových informací

Chcete-li publikovat komerční nabídky k nákupu, musíte přidat informace o výplatách a daních a odeslat je k ověření v Centru pro vývojáře.

**Poskytnutí bankovních informací**

1.  Přihlaste se do [Centra pro vývojáře Microsoft](https://dev.windows.com/registration?accountprogram=azure) pomocí svého účtu Microsoft.
2.  V levé nabídce vyberte **Výplatní účet** v části Zvolit **způsob platby**, vyberte **Bankovní účet** nebo **PayPal**.

    >[!NOTE]
    >Pokud máte komerční nabídky, které zákazníci nakupují na Marketplace, jedná se o účet, na kterém obdržíte výplatu za tyto nákupy.
3.  Zadejte informace o platbě a pak vyberte **Uložit**.

    >[!IMPORTANT]
    >Pokud potřebujete aktualizovat nebo změnit výplatní účet, nahraďte aktuální informace novými informacemi podle předchozích kroků.
    >
    >Změna výplatního účtu může zpozdit platby až o jeden platební cyklus. K tomuto zpoždění dochází, protože potřebujeme ověřit změnu účtu, stejně jako jsme to udělali při prvním nastavení výplatního účtu. Po ověření účtu vám bude vyplacena celá částka. všechny platby splatné pro aktuální platební cyklus budou přidány do dalšího.

4.  Vyberte **další**.

**Poskytnutí daňových informací**

1.  Přihlaste se do [Centra pro vývojáře Microsoft](https://dev.windows.com/registration?accountprogram=azure) pomocí svého účtu Microsoft (v případě potřeby).
2.  V levé nabídce vyberte **Daňový profil**.
3.  Na stránce **Nastavit daňový formulář:**
    - Vyberte zemi nebo oblast, kde máte trvalý pobyt.
    - Vyberte zemi nebo oblast, ve které máte primární občanství.
    - Vyberte **další**.
4.  Zadejte daňové údaje a pak vyberte **Další**.

>[!WARNING]
>Nebudete moci tlačit na své komerční nabídky do produkčního prostředí bez uvedení bankovního účtu a daňových informací na účtu Microsoft Developer Center.

### <a name="developer-center-registration-issues"></a>Problémy s registrací centra pro vývojáře

Pokud máte problémy s registrací Centra pro vývojáře, otevřete lístek podpory pomocí následujících kroků.

1.  Přejděte na [odkaz podpory](https://developer.microsoft.com/windows/support).
2.  V části **Kontaktujte nás**vyberte **Odeslat incident**.
    ![Otevřít lístek](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  V **yberte Pro typ problém**, vyberte "Nápověda s Dev Center" a kategorie , vyberte "Publikovat a spravovat aplikace". **Category** Vyberte **Spustit e-mail**.

    ![identifikovat typ problému](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Dostanete přihlašovací stránku. K přihlášení použijte libovolný účet Microsoft. Pokud nemáte účet Microsoft, [vytvořte si ho](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).\

5.  Zadejte podrobné informace o problému a výběrem **možnosti Odeslat** odešlete letenku.

    ![odeslání jízdenky](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrace účtu na portálu cloudových partnerů

[Portál cloudových partnerů](https://cloudpartner.azure.com/) používáte k publikování a správě nabídek.

1.  Otevřete novou relaci procházení Chrome Incognito nebo Internet Explorer InPrivate, abyste měli na paměti, že nejste přihlášeni k osobnímu účtu.
2.  Přejděte na [portál pro partnery cloudu](https://cloudpartner.azure.com/).
3.  Pokud jste nový uživatel a poprvé se přihlásíte k [portálu cloudových partnerů,](https://cloudpartner.azure.com/) musíte se přihlásit pomocí stejného e-mailového id, které je registrované u vašeho účtu Dev Center. Tím zajistíte, že váš účet Dev Center a účet portálu cloudového partnera jsou vzájemně propojeny.

Později můžete přidat další členy společnosti, kteří pracují na aplikaci. Můžete je jako přispěvatelé nebo vlastníci na portálu partnerů cloudu podle kroků v další části.

Pokud jste přidáni jako přispěvatel/vlastník na portálu portálu cloudového partnera, můžete se přihlásit pomocí vlastního účtu.

>[!TIP]
>Zásady účasti jsou popsané na webu Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Správa uživatelů jako vlastníků nebo přispěvatelů na portálu partnerů cloudu

[Postup správy uživatelů na portálu cloudových partnerů](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Další kroky

Teď, když je váš účet vytvořený a registrovaný, můžete zahájit proces publikování azure marketplace.
