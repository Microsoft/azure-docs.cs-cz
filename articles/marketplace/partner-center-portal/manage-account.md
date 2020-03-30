---
title: Jak spravovat účet Commercial Marketplace v Partnerském centru
description: Přečtěte si, jak spravovat účet Commercial Marketplace v Partnerském centru.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 6d3952c38703d8102a45c4117e3c59e3fa464957
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275811"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Správa účtu komerčního marketplace v Partnerském centru

Po vytvoření [účtu Partnerského centra](./create-account.md)můžete svůj účet a nabídky spravovat pomocí [řídicího panelu Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

V tomto článku se podíváme na to, jak spravovat váš účet v Partnerském centru, včetně toho, jak:

- [Přístup k nastavení účtu Partnerského centra](#access-your-account-settings)
- [Vyhledání ID vydavatele, ID aplikace Symantec, ID prodejce, ID uživatele, ID MPN a tenantů Azure AD](#account-details)
- [Aktualizovat kontaktní informace](#contact-info)
- [Nastavení identifikátorů GUID sledování pro sledování využití zákazníka](#tracking-guids)
- [Spravovat uživatele](#manage-users)
- [Správa skupin](#manage-groups)
- [Správa aplikací Azure AD](#manage-azure-ad-applications)
- [Definování rolí a oprávnění uživatelů](#define-user-roles-and-permissions)
- [Správa klientů Azure AD (pracovní účty)](#manage-tenants)
- [Správa smluv s partnerským centrem](#agreements)

## <a name="access-your-account-settings"></a>Přístup k nastavení účtu

Pokud jste tak ještě neučinili, měli byste vy (nebo správce vaší organizace) přistupovat k [nastavení účtu](https://partner.microsoft.com/dashboard/account/management) pro váš účet Partnerského centra:
- Kontrola stavu ověření účtu vaší společnosti
- Potvrďte své ID symantec, ID prodejce, ID MPN, ID vydavatele a kontaktní informace, včetně schvalovatele společnosti a kontaktu prodejce
- Vytvoření uživatelských účtů pro každého, kdo bude používat váš firemní účet v Centru partnerů

### <a name="open-developer-settings"></a>Otevřít nastavení vývojáře

Nastavení účtu se nachází v pravém horním rohu [řídicího panelu Komerční tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace) v Centru partnerů. Vyberte ikonu ozubeného kola (v pravém horním rohu řídicího panelu) a pak vyberte **Nastavení vývojáře**.

![Nabídka Nastavení účtu v Centru partnerů](./media/dashboard-developer-settings.png)

V **nastavení účtu**budete moci zobrazit:
- **Podrobnosti o účtu**: Typ účtu a stav účtu
- **ID vydavatele:** ID prodejce, ID uživatele, ID vydavatele, klienty Azure AD atd.
- **Kontaktní údaje:** Zobrazované jméno vydavatele, jméno kontaktu prodejce, e-mail, telefon a adresa
- **Sledování identifikátorů GUID**: Všechny identifikátory GUID sledování přidruží k vašemu účtu

### <a name="account-details"></a>Podrobnosti o účtu

V části Podrobnosti o účtu se zobrazí základní informace, jako je **typ účtu** (společnost nebo jednotlivec) a **stav ověření** vašeho účtu. Během procesu ověření účtu se v těchto nastaveních zobrazí každý požadovaný krok, včetně ověření e-mailem, ověření zaměstnání a ověření firmy. Můžete také aktualizovat svůj e-mail zde a v případě potřeby znovu odeslat ověření.

### <a name="publisher-ids"></a>ID aplikace Publisher

V části ID vydavatele se zobrazí **id aplikace Symantec**, **ID prodejce**, **ID uživatele**, **ID MPN**a **klienti Azure AD**. Tyto hodnoty jsou přiřazeny společností Microsoft k jednoznačné identifikaci vývojářského účtu a nelze je upravovat.

### <a name="contact-info"></a>Kontaktní údaje

V části Kontaktní údaje můžete zobrazit **zobrazované jméno aplikace Publisher**, **kontaktní údaje prodejce** (kontaktní jméno, e-mail, telefonní číslo a adresu prodejce společnosti) a **schvalovatele společnosti** (jméno, e-mail a telefonní číslo osoby s oprávněním schvalovat rozhodnutí pro společnost).

#### <a name="payout-account"></a>Výplatní účet

Výplatní účet je bankovní účet, na který jsou odesílány výnosy z prodeje. Tento bankovní účet musí být ve stejné zemi, ve které jste si zaregistrovali účet v Partnerském centru.

Chcete-li nastavit výplatní účet, musíte **svůj účet Microsoft přidružit**:
1. Přejděte na [stránku přehledu komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) v Centru partnerů.
2. V části Profil vyberte **Přidružit svůj účet Microsoft**.
3. Po zobrazení výzvy se přihlaste pomocí svého účtu Microsoft (MSA). Tento účet již nelze přidružit k jinému účtu Centra partnerského centra.
4. Chcete-li dokončit nastavení výplatního účtu, odhlaste se úplně mimo Centrum partnerů a pak se znovu přihlaste pomocí svého účtu Microsoft (nikoli pomocí pracovního účtu).

Nyní, když je váš účet Microsoft přidružený, abyste přidali výplatní účet, budete muset:
- **Zvolte způsob platby**: Bankovní účet nebo PayPal
- **Přidání platebních údajů**: To může zahrnovat výběr typu účtu (kontrola nebo úspora), zadání jména držitele účtu, čísla účtu a čísla směrování, fakturační adresy, telefonního čísla nebo e-mailové adresy PayPal. *Další informace o používání služby PayPal jako způsobu platby účtu a informace o tom, zda je podporována ve vaší oblasti trhu, naleznete v [tématu PayPal info](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Změna výplatního účtu může zpozdit platby až o jeden platební cyklus. K tomuto zpoždění dochází, protože potřebujeme ověřit změnu účtu, stejně jako při prvním nastavení výplatního účtu. Po ověření účtu vám bude vyplacena celá částka. všechny platby splatné pro aktuální platební cyklus budou přidány do dalšího.  

#### <a name="tax-profile"></a>Daňový profil

Zkontrolujte aktuální stav daňového profilu a potvrdíte, že se zobrazí správný **typ entity** a informace **o daňovém certifikátu.** Vyberte **Upravit,** chcete-li aktualizovat nebo vyplnit všechny požadované formuláře.

Chcete-li zjistit svůj daňový status, musíte uvést zemi svého bydliště a státní občanství a vyplnit příslušné daňové formuláře spojené s vaší zemí nebo oblastí.

Bez ohledu na zemi vašeho bydliště nebo občanství musíte vyplnit daňové formuláře Spojených států, abyste mohli prodávat všechny nabídky prostřednictvím společnosti Microsoft. Partneři, kteří splňují určité požadavky na pobyt ve Spojených státech, musí vyplnit formulář IRS W-9. Ostatní partneři mimo Spojené státy musí vyplnit formulář IRS W-8. Tyto formuláře můžete vyplnit online po vyplnění daňového profilu.

Identifikační číslo daňového poplatníka (nebo ITIN) ve Spojených státech amerických není nutné přijímat platby od společnosti Microsoft nebo žádat o výhody daňové smlouvy.

Daňové formuláře můžete vyplnit a podat elektronicky v Partnerském centru; ve většině případů není nutné tisknout a zasílat žádné formuláře.

Různé země a regiony mají různé daňové požadavky. Přesná částka, kterou musíte zaplatit na daních, závisí na zemích a oblastech, kde nabídky prodáváte. Společnost Microsoft v některých zemích ukázňuje daň z prodeje a použití vaším jménem. Tyto země budou identifikovány v procesu uvedení vaší nabídky. V jiných zemích, v závislosti na tom, kde jste registrováni, může být nutné provést daň z prodeje a použít daň z prodeje přímo místnímu daňovému úřadu. Výnosy z prodeje, které obdržíte, mohou být navíc zdanitelné jako příjem. Důrazně doporučujeme, abyste se obrátili na příslušný úřad pro vaši zemi nebo oblast, který vám může nejlépe pomoci určit správné daňové údaje pro vaše prodejní transakce společnosti Microsoft.

##### <a name="withholding-rates"></a>Srážkové sazby
Údaje, které zadáte ve svých daňových formulářích, určují příslušnou sazbu srážkové daně. Srážková sazba se vztahuje pouze na prodeje, které provedete do Spojených států; prodeje uskutečněné do poboček mimo USA nepodléhají srážkové účasti. Sazby srážkové daně se liší, ale pro většinu vývojářů, kteří se registrují mimo Spojené státy, je výchozí sazba 30%. Máte možnost snížit tuto sazbu, pokud vaše země souhlasila se smlouvou o dani z příjmu se Spojenými státy.

##### <a name="tax-treaty-benefits"></a>Výhody daňové smlouvy
Pokud se nejste mimo Spojené státy, můžete využít výhod daňových smluv. Tyto výhody se v jednotlivých zemích liší a mohou vám umožnit snížit výši daní, které společnost Microsoft zadržuje. O výhody daňové smlouvy můžete žádat vyplněním části II formuláře W-8BEN. Doporučujeme, abyste komunikovali s příslušnými zdroji ve vaší zemi nebo oblasti a zjistili, zda se na vás tyto výhody vztahují.

[Přečtěte si další informace o podrobnostech o daních pro vývojáře aplikací a her pro Windows a vydavatele Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Stav blokování výplaty

Ve výchozím nastavení společnost Microsoft odesílá platby měsíčně. Máte však možnost pozdržet výplaty, což zabrání odesílání plateb na váš účet. Pokud se rozhodnete své výplaty pozdržet, budeme i nadále zaznamenávat veškeré příjmy, které získáte, a podrobnosti poskytneme ve **svém souhrnu výplat**. Žádné platby na váš účet však nepošleme, dokud blokování neodeberete. 

Chcete-li platby pozdržet, přejděte do **nastavení účtu**. V části **Finanční podrobnosti**přepněte v části **Stav blokování výplaty** jezdec **na Zapnuto**. Stav podržení výplaty můžete kdykoli změnit, ale uvědomte si, že vaše rozhodnutí bude mít vliv na další měsíční výplatu. Pokud například chcete podržet dubnovou výplatu, nezapomeňte do konce března nastavit stav podržení výplaty **na zapnuto.**

Jakmile nastavíte stav podržení výplaty **na Zapnuto**, budou všechny výplaty pozastaveny, dokud jezdec nepřepnete zpět na **Vypnuto**. Pokud tak učiníte, budete zahrnuti během dalšího měsíčního výplatního cyklu (za předpokladu, že byly splněny příslušné limity plateb). Pokud jste například měli výplaty pozastavené, ale chtěli byste, aby výplata byla vygenerována v červnu, nezapomeňte do konce května přepnout stav podržení výplaty na **Vypnuto.**

> [!NOTE]
> Výběr **stavu výplaty se** vztahuje na **všechny** zdroje příjmů, které se platí prostřednictvím Centra microsoft partnerů, včetně Azure Marketplace, AppSource, Microsoft Store, inzerce atd.). Pro každý zdroj výnosů nelze vybrat různé stavy blokování.

### <a name="devices"></a>Zařízení

Nastavení správy zařízení platí pouze pro publikování upw. [Další informace](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Sledování identifikátorů GUID

Globálně jedinečné identifikátory (GUID) jsou jedinečná referenční čísla (s 32 šestnáctkovými číslicemi), která lze použít ke sledování využití Azure. 

Chcete-li vytvořit identifikátory GUID pro sledování, měli byste použít generátor identifikátorů GUID. Tým služby Azure Storage vytvořil [formulář generátoru IDENTIFIKÁTORŮ GUID,](https://aka.ms/StoragePartners) který vám pošle e-mail s identifikátorem GUID správného formátu a může být znovu použit v různých sledovacích systémech.

Doporučujeme vytvořit jedinečný identifikátor GUID pro každou nabídku a distribuční kanál pro každý produkt. Pokud nechcete, aby bylo hlášení rozděleno, můžete použít jeden identifikátor GUID pro více distribučních kanálů produktu.

Pokud nasadíte produkt pomocí šablony a je k dispozici na Azure Marketplace i na GitHubu, můžete vytvořit a zaregistrovat 2 odlišné identifikátory GUID:

*   Produkt A na Azure Marketplace
*   Produkt A na GitHubu

Vytváření sestav se provádí podle hodnoty partnera (Microsoft Partner ID) a identifikátorů GUID. Můžete také sledovat identifikátory GUID na podrobnější úrovni, která se zarovná každému plánu v rámci vaší nabídky.

Další informace naleznete [v nejčastějších dotazech ke sledování využití zákazníků azure pomocí identifikátorů GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).

## <a name="create-a-billing-profile"></a>Vytvoření fakturačního profilu

Pokud publikujete nabídku [Dynamics 365 for Customer Engagement & Power Apps](./create-new-customer-engagement-offer.md) nebo Dynamics [365 for Operations,](./create-new-operations-offer.md) je třeba vyplnit **fakturační profil**.

Fakturační adresa je předem vyplněna od právnické osoby a tuto adresu můžete později aktualizovat. Pole DANĚ a DIČ jsou nepovinná.  Název země a název společnosti nelze upravovat.

## <a name="multi-user-account-management"></a>Správa účtů pro více uživatelů

Centrum partnerů využívá [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) pro přístup k účtům a správu více uživatelů. Azure AD vaší organizace je automaticky přidružena k účtu Partnerského centra jako součást procesu registrace.

## <a name="manage-users"></a>Správa uživatelů

V části **Uživatelé** v Centru partnerů (v části **Nastavení účtu)** můžete pomocí Azure AD spravovat uživatele, skupiny a aplikace Azure AD, které mají přístup k vašemu účtu Partnerského centra. Chcete-li spravovat uživatele, musíte být přihlášeni pomocí [svého pracovního účtu](./company-work-accounts.md) (přidruženého klienta Azure AD). Chcete-li spravovat uživatele v rámci jiného pracovního účtu / tenanta, budete se muset odhlásit a znovu se přihlásit jako uživatel s oprávněními **správce** pro tento pracovní účet / tenanta.

Jakmile jste přihlášeni pomocí pracovního účtu (tenantA Azure AD), můžete:
- [Přidání nebo odebrání uživatelů](#add-or-remove-users)
- [Změna uživatelského hesla](#change-a-user-password)
- [Přidání nebo odebrání skupin](#add-or-remove-users)
- [Přidání nebo odebrání aplikací Azure AD](#add-new-azure-ad-applications)
- [Správa klíčů pro aplikaci Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definování rolí a oprávnění uživatelů](#define-user-roles-and-permissions)

Mějte na paměti, že všichni uživatelé Partnercenter (včetně skupin a aplikací Azure AD) musí mít aktivní pracovní účet v [tenantovi Azure AD,](#manage-tenants) který je přidružený k vašemu účtu Partner Center.

### <a name="add-or-remove-users"></a>Přidání nebo odebrání uživatelů

Váš účet musí mít oprávnění [**na úrovni správce**](#define-user-roles-and-permissions) pro pracovní účet [(tenant Azure AD),](./company-work-accounts.md) ve kterém chcete přidat nebo upravit uživatele.

#### <a name="add-existing-users"></a>Přidání stávajících uživatelů

Přidání uživatelů do účtu Partnerského centra, které už existují v pracovním účtu vaší společnosti [(tenant Azure AD),](./company-work-accounts.md):

1. Přejděte na **Uživatelé** (v části **Nastavení účtu)** a vyberte **Přidat uživatele**.
2. Ze seznamu, který se zobrazí, vyberte jednoho nebo více uživatelů. Pomocí vyhledávacího pole můžete vyhledat konkrétní uživatele.
*Pokud vyberete více než jednoho uživatele, který chcete přidat do svého účtu V centru partnerských, musíte mu přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více uživatelů s různými rolemi nebo oprávněními, opakujte tyto kroky pro každou roli nebo sadu vlastních oprávnění.
3. Po dokončení výběru uživatelů klepněte na **tlačítko Přidat vybrané**.
4. V části **Role** zadejte role nebo přizpůsobená oprávnění pro vybrané uživatele.
5. Vyberte **Uložit**.

#### <a name="create-new-users"></a>Vytvořit nové uživatele

Chcete-li vytvořit zcela nové uživatelské účty, musíte mít účet s oprávněními [**globálního správce.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

1. Přejděte na **Uživatelé** (v části **Nastavení účtu**), vyberte **Přidat uživatele**a pak zvolte Vytvořit **nové uživatele**.
1. Zadejte jméno, příjmení a uživatelské jméno pro každého nového uživatele. 
1. Pokud chcete, aby nový uživatel měl v adresáři vaší organizace účet globálního správce, zaškrtněte políčko **Uvažovat: Uděláte z tohoto uživatele globálního správce ve službě Azure AD s úplnou kontrolou nad všemi prostředky adresáře**. To uživateli poskytne úplný přístup ke všem funkcím pro správu ve službě Azure AD vaší společnosti. Budou moct přidávat a spravovat uživatele v pracovním účtu vaší organizace (tenant Azure AD), i když ne v Centru partnerů, pokud mu neudělíte příslušnou roli/oprávnění.
1. Pokud jste zaškrtli **políčko, aby se tento uživatel globální správce**, budete muset zadat **e-mail pro obnovení hesla** pro uživatele obnovit své heslo v případě potřeby.
1. V části Členství ve **skupině** vyberte všechny skupiny, do kterých má nový uživatel patřit.
1. V části **Role** zadejte role nebo přizpůsobená oprávnění pro uživatele.
1. Vyberte **Uložit**.

Vytvoření nového uživatele v Centru partnerských center také vytvoří účet pro tohoto uživatele v pracovním účtu (tenant Azure AD), ke kterému jste přihlášeni. Provádění změn jména uživatele v Partnerském centru provede stejné změny v pracovním účtu vaší organizace (tenant Azure AD).

#### <a name="invite-new-users-by-email"></a>Pozvání nových uživatelů e-mailem

Chcete-li pozvat uživatele, kteří nejsou aktuálně součástí vašeho firemního pracovního účtu (tenanta Azure AD) prostřednictvím e-mailu, musíte mít účet s oprávněními [**globálního správce.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

1. Přejděte na **Uživatelé** (v části **Nastavení účtu**), vyberte **Přidat uživatele**a pak zvolte **Pozvat uživatele e-mailem**.
2. Zadejte jednu nebo více e-mailových adres (až deset) oddělených čárkami nebo středníky.
3. V části **Role** zadejte role nebo přizpůsobená oprávnění pro uživatele.
4. Vyberte **Uložit**.

Pozvaní uživatelé obdrží e-mailovou pozvánku, aby se připojili k vašemu účtu partnerského centra. Nový účet hosta uživatele se vytvoří ve vašem pracovním účtu (tenant Azure AD). Každý uživatel bude muset přijmout pozvánku, aby měl přístup k vašemu účtu.

Pokud potřebujete pozvánku odeslat znovu, navštivte stránku **Uživatelé,** vyhledejte pozvánku v seznamu uživatelů, vyberte jejich e-mailovou adresu (nebo text s textem, ve které *je uvedeno Pozvání čekající na vyřízení*). Potom v dolní části stránky vyberte **Znovu odeslat pozvánku**.

> [!NOTE]
> Pokud vaše organizace používá [integraci adresářů](https://go.microsoft.com/fwlink/p/?LinkID=724033) k synchronizaci místní adresářové služby s azure ad, nebudete moct vytvářet nové uživatele, skupiny nebo aplikace Azure AD v Centru partnerů. Vy (nebo jiný správce v místním adresáři) je budete muset vytvořit přímo v místním adresáři, než je budete moct zobrazit a přidat v Centru partnerů.

#### <a name="remove-a-user"></a>Odebrání uživatele

Pokud chcete odebrat uživatele ze svého pracovního účtu (tenant Azure AD), přejděte na **Users** (v části **Nastavení účtu),** vyberte uživatele, kterého chcete odebrat, pomocí zaškrtávacího políčka ve sloupci zcela vpravo a pak zvolte **Odebrat** z dostupných akcí. Zobrazí se vyskakovací okno, které potvrzuje, že chcete vybraného uživatele odebrat.

#### <a name="change-a-user-password"></a>Změna uživatelského hesla

Pokud jeden z vašich uživatelů potřebuje změnit své heslo, může tak učinit sami, pokud jste při vytváření uživatelského účtu zadali **e-mail pro obnovení hesla.** Heslo uživatele můžete také aktualizovat podle následujících kroků. Chcete-li změnit heslo uživatele ve vašem pracovním účtu společnosti (tenant Azure AD), musíte být přihlášeni k účtu s oprávněními [**globálního správce.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) Všimněte si, že tím se změní heslo uživatele ve vašem tenantovi Azure AD, spolu s heslem, které používají pro přístup k Centru partnerů.

1. Na stránce **Uživatelé** (v části **Nastavení účtu)** vyberte název uživatelského účtu, který chcete upravit.
2. V dolní části stránky vyberte tlačítko **Obnovit heslo.**
3. Zobrazí se potvrzovací stránka s přihlašovacími údaji uživatele, včetně dočasného hesla. Nezapomeňte tyto informace vytisknout nebo zkopírovat a poskytnout je uživateli, protože po opuštění této stránky nebudete mít přístup k dočasnému heslu.

## <a name="manage-groups"></a>Správa skupin

Skupiny umožňují řídit více uživatelských rolí a oprávnění dohromady.

#### <a name="add-an-existing-group"></a>Přidání existující skupiny

Přidání skupiny, která už v pracovním účtu vaší organizace (tenant Azure AD) existuje, do účtu Partnerského centra:

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat skupiny**.
2. Ze seznamu, který se zobrazí, vyberte jednu nebo více skupin. Pomocí vyhledávacího pole můžete vyhledat určité skupiny.
Pokud vyberete více než jednu skupinu, kterou chcete přidat do účtu Partnerského centra, musíte jim přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více skupin s různými rolemi nebo oprávněními, opakujte tyto kroky pro každou roli nebo sadu vlastních oprávnění.
3. Po dokončení výběru skupin klepněte na **tlačítko Přidat vybrané**.
4. V části **Role** zadejte role nebo přizpůsobená oprávnění pro vybrané skupiny. Všichni členové skupiny budou mít přístup k vašemu účtu Partnerského centra s oprávněními, která pro skupinu použijete, bez ohledu na role a oprávnění přidružená k jejich individuálnímu účtu.
5. Vyberte **Uložit**.

Přidáte-li existující skupinu, bude mít každý uživatel, který je členem této skupiny, přístup k vašemu účtu Partnerského centra s oprávněními přidruženými k přiřazené roli skupiny.

#### <a name="add-a-new-group"></a>Přidání nové skupiny

Přidání zcela nové skupiny do účtu Partnerského centra:

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat skupiny**.
2. Na další stránce vyberte **Nová skupina**.
3. Zadejte zobrazovaný název nové skupiny.
4. Zadejte role nebo přizpůsobená oprávnění pro skupinu. Všichni členové skupiny budou mít přístup k vašemu účtu Partnerského centra s oprávněními, která zde použijete, bez ohledu na role/oprávnění přidružená k jejich individuálnímu účtu.
5. Ze seznamu, který se zobrazí, vyberte uživatele pro novou skupinu. Pomocí vyhledávacího pole můžete vyhledat konkrétní uživatele.
6. Po dokončení výběru uživatelů klikněte na **Přidat,** abyste je přidali do nové skupiny.
7. Vyberte **Uložit**.

Všimněte si, že tato nová skupina se vytvoří v pracovním účtu vaší organizace (tenant Azure AD) také, nejen ve vašem účtu Partnerského centra.

#### <a name="remove-a-group"></a>Odebrání skupiny

Pokud chcete odebrat skupinu ze svého pracovního účtu (tenant Azure AD), přejděte na **Users** (v části **Nastavení účtu),** vyberte skupinu, kterou chcete odebrat, pomocí zaškrtávacího políčka ve sloupci zcela vpravo a pak zvolte **Odebrat** z dostupných akcí. Zobrazí se vyskakovací okno, které potvrzuje, že chcete vybrané skupiny odebrat.

## <a name="manage-azure-ad-applications"></a>Správa aplikací Azure AD

Můžete povolit aplikacím nebo službám, které jsou součástí služby Azure AD vaší společnosti, přístup k účtu Partnercenter.

#### <a name="add-existing-azure-ad-applications"></a>Přidání existujících aplikací Azure AD

Přidání aplikací, které už ve službě Azure Active Directory vaší společnosti existují:

1. Na stránce **Uživatelé** (v části **Nastavení účtu)** vyberte **Přidat aplikace Azure AD**.
2. Vyberte jednu nebo více aplikací Azure AD ze seznamu, který se zobrazí. Vyhledávací pole můžete použít k vyhledání konkrétních aplikací Azure AD. Pokud vyberete více než jednu aplikaci Azure AD, kterou chcete přidat do účtu Partnerského centra, musíte jim přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více aplikací Azure AD s různými rolemi nebo oprávněními, opakujte tyto kroky pro každou roli nebo sadu vlastních oprávnění.
3. Po dokončení výběru aplikací Azure AD klikněte na **Přidat vybrané**.
4. V části **Role** zadejte role nebo přizpůsobená oprávnění pro vybrané aplikace Azure AD.
5. Vyberte **Uložit**.

#### <a name="add-new-azure-ad-applications"></a>Přidání nových aplikací Azure AD

Pokud chcete udělit Partner Center přístup ke zbrusu nový účet aplikace Azure AD, můžete vytvořit v části **Uživatelé.** Všimněte si, že tím se vytvoří nový účet ve vašem firemním pracovním účtu (tenant Azure AD), nejen ve vašem účtu Partnerského centra. Pokud používáte především tuto aplikaci Azure AD pro ověřování v Centru partnerských center a nepotřebujete, aby k ní uživatelé měli přímý přístup, můžete zadat libovolnou platnou adresu adresy **URL odpovědi** a **identifikátoru URI ID aplikace**, pokud tyto hodnoty nepoužívají žádná jiná aplikace Azure AD ve vašem adresáři.

1. Na stránce **Uživatelé** (v části **Nastavení účtu)** vyberte **Přidat aplikace Azure AD**.
2. Na další stránce vyberte **nová aplikace Azure AD**.
3. Zadejte **adresu URL odpovědi** pro novou aplikaci Azure AD. Toto je adresa URL, kde se uživatelé můžou přihlásit a používat vaši aplikaci Azure AD (někdy označovanou také jako adresa URL aplikace nebo adresa URL přihlášení). **Adresa URL odpovědi** nesmí být delší než 256 znaků a musí být v adresáři jedinečná.
4. Zadejte **identifikátor URI ID aplikace** pro novou aplikaci Azure AD. Toto je logický identifikátor pro aplikaci Azure AD, která se zobrazí při odeslání požadavku na jednotné přihlášení do služby Azure AD. Všimněte si, že **identifikátor URI ID aplikace** musí být jedinečný pro každou aplikaci Azure AD ve vašem adresáři. Toto ID nesmí být delší než 256 znaků. Další informace o identifikátoru URI ID aplikace najdete v [tématu Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5. V části **Role** zadejte role nebo přizpůsobená oprávnění pro aplikaci Azure AD.
6. Vyberte **Uložit**.

Po přidání nebo vytvoření aplikace Azure AD se můžete vrátit do části **Uživatelé** a vybrat název aplikace, který chcete zkontrolovat nastavení aplikace, včetně ID klienta, ID klienta, adresy URL odpovědi a identifikátoru URI ID aplikace.

#### <a name="remove-an-application"></a>Odebrání aplikace

Pokud chcete odebrat aplikaci z vašeho pracovního účtu (tenant Azure AD), přejděte na **Users** (v části **Nastavení účtu**), vyberte aplikaci, kterou chcete odebrat pomocí zaškrtávacího políčka ve sloupci zcela vpravo, a pak zvolte **Odebrat** z dostupných akcí. Zobrazí se vyskakovací okno, které potvrzuje, že chcete vybrané aplikace odebrat.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Správa klíčů pro aplikaci Azure AD

Pokud vaše aplikace Azure AD čte a zapisuje data ve službě Microsoft Azure AD, bude potřebovat klíč. Klíče pro aplikaci Azure AD můžete vytvořit úpravou jejích informací v Centru partnerů. Můžete také odebrat klíče, které již nejsou potřeba.

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte název aplikace Azure AD. Uvidíte všechny aktivní klíče pro aplikaci Azure AD, včetně data, kdy byl klíč vytvořen a kdy vyprší jeho platnost. 
2. Pokud chcete odebrat klíč, který už není potřeba, vyberte **Odebrat**.
3. Chcete-li přidat nový klíč, vyberte **Přidat nový klíč**.
4. Zobrazí se obrazovka s **ID klienta** a **hodnotami klíče**. Nezapomeňte tyto informace vytisknout nebo zkopírovat, protože po opuštění této stránky k nim nebudete mít přístup znovu.
5. Pokud chcete vytvořit další klíče, vyberte **Přidat další klíč**.

## <a name="define-user-roles-and-permissions"></a>Definování rolí a oprávnění uživatelů

Uživatelům vaší společnosti mohou být přiřazeny následující role a oprávnění pro program Commercial Marketplace v Centru partnerů:

- **Manager**
  - Přístup ke všem funkcím účtu Microsoft kromě nastavení daní a výplat
  - Může spravovat uživatele, role a pracovní účty (klienty)
- **Developer**
  - Může spravovat a publikovat nabídky
  - Lze zobrazit některé sestavy vydavatelů.

> [!NOTE]
> Pro komerční marketplace se role Globální správce, Obchodní přispěvatel, Finanční přispěvatel a Marketér nepoužívají. Přiřazení těchto rolí uživatelům nemá žádný vliv. Pouze role Správce a Vývojář udělují oprávnění uživatelům.

Další informace o správě rolí a oprávnění v jiných oblastech Centra partnerů, jako je Azure Active Directory (AD), Zprostředkovatel cloudových řešení (CSP), Dodavatel ovládacích panelů (CPV), Uživatelé typu Host nebo Microsoft Partner Network (MPN), najdete [v tématu Přiřazení rolí a oprávnění uživatelům v Centru partnerů](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Správa klientů

Tenant Azure Active Directory (AD), označovaný také jako váš "pracovní účet" v této dokumentaci, je reprezentace vaší organizace nastavené na webu Azure Portal a pomáhá vám spravovat konkrétní instanci cloudových služeb Microsoftu pro vaše interní a externími uživateli. Pokud se vaše organizace přihlásila k odběru cloudové služby Microsoftu, jako je Azure, Microsoft Intune nebo Office 365, byl pro vás vytvořen klient Azure AD.

Můžete nastavit více klientů pro použití s Partnerským centrem. Každý uživatel s rolí **Správce** v účtu Partnerského centra bude mít možnost přidat a odebrat klienty Azure AD z účtu.  

### <a name="add-an-existing-tenant"></a>Přidání existujícího klienta

Přidružení jiného klienta Azure AD k účtu Partnerského centra:

1. Na stránce **Klienti** (v části **Nastavení účtu**) vyberte **Přidružit jiného klienta Azure AD**.
2. Zadejte svoje přihlašovací údaje Azure AD pro klienta, kterého chcete přidružit.
3. Zkontrolujte název organizace a domény pro vašeho klienta Azure AD. Chcete-li přidružení dokončit, vyberte **možnost Potvrdit**.

Pokud je přidružení úspěšné, budete připraveni přidávat a spravovat uživatele účtu v části **Uživatelé** v Centru partnerů.

### <a name="create-a-new-tenant"></a>Vytvoření nového klienta

Vytvoření zcela nového klienta Azure AD pomocí účtu Partnerského centra:

1. Na stránce **Klienti** (v části **Nastavení účtu**) vyberte **Vytvořit nového klienta Azure AD**.
2. Zadejte informace o adresáři pro nové azure ad:
    - **Název domény**: Jedinečný název, který budeme používat pro vaši doménu Azure AD, spolu s ".onmicrosoft.com". Například pokud jste zadali "příklad", vaše doména Azure AD by "example.onmicrosoft.com".
    - **Kontaktní e-mail**: E-mailová adresa, na které vás můžeme v případě potřeby kontaktovat ohledně vašeho účtu.
    - **Informace o uživatelském účtu globálního správce**: Křestní jméno, příjmení, uživatelské jméno a heslo, které chcete použít pro nový účet globálního správce.
3. Vyberte **Vytvořit,** chcete-li potvrdit novou doménu a informace o účtu.
4. Přihlaste se pomocí nového uživatelského jména a hesla globálního správce Azure AD a začněte [přidávat a spravovat uživatele](#manage-users).

Další informace o vytváření nových klientů na portálu Azure, nikoli prostřednictvím portálu Partnercenter, najdete v článku [Vytvoření nového klienta ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Odebrání klienta

Pokud chcete odebrat klienta z účtu Partnerského centra, vyhledejte jeho název na stránce **Klienti** (v **nastavení účtu**) a vyberte **Odebrat**. Budete vyzváni k potvrzení, že chcete odebrat klienta. Jakmile tak učiníte, nebudou se moci k účtu Partnerského centra přihlásit žádní uživatelé v tomto tenantovi a všechna oprávnění, která jste pro tyto uživatele nakonfigurovali, budou odebrána.

Při odebrání klienta se k účtu obchodního centra partnerského centra již nebudou moci přihlásit všichni uživatelé, kteří byli přidáni do účtu Partnerského centra.

> [!TIP]
> Klienta nelze odebrat, pokud jste aktuálně přihlášeni do Centra partnerů pomocí účtu ve stejném tenantovi. Chcete-li odebrat klienta, musíte se přihlásit do Centra partnerů jako **správce** pro jiného klienta, který je přidružen k účtu. Pokud je k účtu přidružen pouze jeden klient, lze tohoto klienta odebrat pouze po přihlášení pomocí účtu Microsoft, který účet otevřel.

## <a name="agreements"></a>Smlouvy

V části **Smlouvy** v Centru partnerů (v části **Nastavení účtu)** si můžete prohlédnout seznam smluv o publikování, které jste autorizovali. Tyto smlouvy jsou uvedeny podle názvu a čísla verze, včetně data, kdy byla přijata, a jména uživatele, který smlouvu přijal.

**Pokud** existují aktualizace smluv, které vyžadují vaši pozornost, mohou se v horní části této stránky zobrazit potřebné akce. Chcete-li přijmout aktualizovanou smlouvu, přečtěte si nejprve propojenou verzi smlouvy a pak vyberte **Přijmout smlouvu**.

## <a name="next-steps"></a>Další kroky

- [Vytvoření nové nabídky SaaS](./create-new-saas-offer.md)
