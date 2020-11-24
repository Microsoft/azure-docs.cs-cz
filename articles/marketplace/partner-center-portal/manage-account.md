---
title: Jak spravovat účet komerčního obchodu Marketplace v partnerském centru Microsoftu
description: Naučte se spravovat účet komerčního obchodu Marketplace v partnerském centru Microsoftu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 11/02/2020
ms.openlocfilehash: 8b372cc0fc19c6bf163d9d4e61cde6ce74cb3f28
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95538375"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Správa účtu komerčního tržiště v partnerském centru

Po [Vytvoření účtu partnerského centra](./create-account.md)můžete pomocí [řídicího panelu komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) spravovat svůj účet a nabídky.

V tomto článku se podrobně, jak spravovat účet partnerského centra, včetně toho, jak:

- [Přístup k nastavení účtu partnerského centra](#access-your-account-settings)
- [Vyhledejte ID vydavatele, ID Symantec, ID prodejce, ID uživatele, ID MPN a klienty Azure AD.](#account-details)
- [Aktualizovat kontaktní informace](#contact-info)
- [Nastavení sledovacích identifikátorů GUID pro sledování využití zákazníky](#tracking-guids)
- [Správa uživatelů](#manage-users)
- [Správa skupin](#manage-groups)
- [Správa aplikací Azure AD](#manage-azure-ad-applications)
- [Definování rolí a oprávnění uživatelů](#define-user-roles-and-permissions)
- [Správa tenantů Azure AD (pracovní účty)](#manage-tenants)
- [Spravovat smlouvy partnerského centra](#agreements)

## <a name="access-your-account-settings"></a>Přístup k nastavení účtu

Pokud jste to ještě neudělali, měli byste vy (nebo správce vaší organizace) získat přístup k [Nastavení účtu](https://partner.microsoft.com/dashboard/account/management) pro účet partnerského centra:

- Kontrola stavu ověření účtu vaší společnosti.
- Potvrďte své ID Symantec, ID prodejce, ID Microsoft Partner Network (MPN), ID vydavatele a kontaktní údaje, včetně kontaktu schvalovatele společnosti a prodejce.
- Vytvořte uživatelské účty pro kohokoli, kdo bude používat váš obchodní účet v partnerském centru.

### <a name="open-account-settings"></a>Otevřít nastavení účtu

Na [řídicím panelu komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace) v partnerském centru vyberte **Nastavení** (ikona ozubeného kolečka) v pravém horním rohu a pak klikněte na **Nastavení účtu**.

![Nabídka nastavení účtu v partnerském centru](./media/dashboard-developer-settings.png)

V **Nastavení účtu** můžete zobrazit vaše:

- **Podrobnosti účtu**: typ účtu a stav účtu
- **ID vydavatele**: ID prodejce, ID uživatele, ID vydavatele, klienty Azure AD atd.
- **Kontaktní údaje**: zobrazované jméno vydavatele, jméno kontaktní osoby, e-mail, telefon a adresa
- **Sledovací identifikátory GUID**: jakékoli sledovací identifikátory GUID přidružit k vašemu účtu

### <a name="account-details"></a>Podrobnosti účtu

V části Podrobnosti o účtu uvidíte základní informace, jako je **typ účtu** (společnost nebo jednotlivec) a **stav ověření** vašeho účtu. Během procesu ověřování účtu se tato nastavení zobrazí každý požadovaný krok, včetně ověření e-mailu, ověřování zaměstnanosti a obchodního ověření. V případě potřeby můžete také aktualizovat svůj e-mail a znovu ho ověřit.

### <a name="publisher-ids"></a>ID vydavatele

V části ID vydavatelů uvidíte **ID společnosti Symantec**, **ID prodejce**, **ID uživatele**, **ID MPN** a **klienty Azure AD**. Tyto hodnoty přiřazuje Microsoft, aby jednoznačně identifikovaly váš vývojářský účet a nedají se upravovat.

### <a name="contact-info"></a>Kontaktní údaje

V části kontaktní údaje uvidíte **zobrazované jméno vydavatele**, **kontaktní údaje prodejce** (jméno kontaktu, e-mail, telefonní číslo a adresu prodejce společnosti) a **schvalovatel společnosti** (jméno, e-mail a telefonní číslo osoby s oprávněním ke schválení rozhodnutí společnosti).

#### <a name="payout-account"></a>Účet pro výběr

Účet pro výběr je bankovní účet, na který se z prodeje posílají výnosy. Tento bankovní účet musí být ve stejné zemi nebo oblasti, kde jste zaregistrovali účet partnerského centra.

Postup nastavení účtu výběr:

1. Přejít na [stránku Přehled komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) v partnerském centru.
2. V části profil klikněte vedle možnosti **profil** výběru na možnost **aktualizovat**.
3. **Vyberte způsob platby**: bankovní účet nebo PayPal.
4. **Přidat platební informace**: může to zahrnovat výběr typu účtu (kontroly a úspory), zadání názvu držitele účtu, čísla účtu a čísla směrování, fakturační adresy, telefonního čísla nebo e-mailové adresy PayPal. * Další informace o používání služby PayPal jako způsobu platby na účet a o tom, jestli se podporuje v oblasti vašeho trhu, najdete v tématu [informace o PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Změna účtu výběr může zpozdit vaše platby až do jednoho platebního cyklu. K tomuto zpoždění dochází, protože potřebujeme ověřit změnu účtu, stejně jako při prvním nastavování účtu výběr. Po ověření účtu se pořád bude platit celá částka. jakékoli platby splatné za aktuální platební cyklus budou přidány do dalšího.  

#### <a name="tax-profile"></a>Daňový profil

Zkontrolujte aktuální stav daňového profilu a zobrazí se správné informace o **typu entity** a **daňovém certifikátu** . Vyberte **Upravit** , pokud chcete aktualizovat nebo dokončit všechny požadované formuláře.

Aby bylo možné stanovit svůj daňový stav, musíte určit svou zemi/oblast pobytu a občanství a dokončit příslušné daňové formuláře spojené s vaší zemí nebo oblastí.

Bez ohledu na vaši zemi nebo oblast pobytu nebo občanství musíte vyplnit USA daňové formuláře a prodávat jakékoli nabídky prostřednictvím Microsoftu. Partneři, kteří splňují určité USA požadavky na zaplňování, musí vyplnit formulář finančního úřadu W-9. Jiní partneři mimo USA musí vyplnit formulář finančního úřadu W-8. Tyto formuláře můžete po dokončení finančního profilu vyplnit online.

USA jednotlivé daňové identifikační číslo (nebo ITIN) se nevyžaduje pro příjem plateb od společnosti Microsoft nebo nárokování na výhody daňové smlouvy.

Daňové formuláře můžete v partnerském centru doplňovat a odesílat elektronicky. ve většině případů nemusíte tisknout ani tisknout žádné formuláře.

Různé země a oblasti mají různé daňové požadavky. Přesná částka, kterou musíte zaplatit za daně, závisí na zemích a oblastech, kde prodáváte své nabídky. Společnost Microsoft se zakládá za prodej a v některých zemích nebo oblastech používá daň za vás. Tyto země nebo oblasti budou identifikovány v procesu výpisu vaší nabídky. V jiných zemích nebo oblastech, v závislosti na tom, kde jste registrováni, může být nutné uhradit prodej a daň z prodeje přímo k místní autoritě zdanění. Tržby, které obdržíte, se navíc můžou zdanitelně vyvažovat za příjem. Důrazně doporučujeme, abyste se obrátili na příslušné oprávnění pro vaši zemi nebo oblast, která vám nejlépe pomůže určit správné daňové informace pro prodejní transakce od společnosti Microsoft.

##### <a name="withholding-rates"></a>Míry odmítnutí

Informace, které odesíláte v daňovém formuláři, určují odpovídající míru srážkové srážky. Míra odmítnutí se vztahuje pouze na prodej, který provedete do USA; prodej uskutečněný v jiných umístěních než USA nezávisí na odmítnutí. Sazby pro srážka se liší, ale u většiny vývojářů, kteří registrují mimo USA, je výchozí sazba 30%. Tuto sazbu máte možnost snížit, pokud se země nebo oblast dohodla se smlouvou o dani ze zisku s USA.

##### <a name="tax-treaty-benefits"></a>Výhody daňové smlouvy

Pokud nejste mimo USA, možná budete moct využít výhody daňové smlouvy. Tyto výhody se liší od zemí/oblastí až po zemi nebo oblast a můžou snížit množství daní, které společnost Microsoft zakazuje. Výhody daňové smlouvy můžete uplatnit vyplněním části II formuláře W-8BEN. Doporučujeme, abyste komunikovali s příslušnými prostředky ve vaší zemi nebo oblasti, abyste zjistili, jestli se vám tyto výhody vztahují.

[Přečtěte si další informace o daňových detailech pro vývojáře aplikací a her pro Windows a Azure Marketplace vydavatelům](/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Stav blokování ve výběr

Společnost Microsoft standardně odesílá platby měsíčně. Můžete ale volitelně zadat své výběry, které zabrání odeslání plateb vašemu účtu. Pokud se rozhodnete, že se vaše výběry zablokují, budeme nahrávat všechny výnosy, které získáte, a poskytnout podrobné údaje v **souhrnu** výběru. Do vašeho účtu ale nepošleme žádné platby, dokud ho neodeberete.

Pokud chcete své platby zablokovat, přečtěte si **Nastavení účtu**. V části **finanční informace** v části **stav blokování výběr** přepněte posuvník na **zapnuto**. Stav blokování můžete kdykoli změnit, ale mějte na paměti, že vaše rozhodnutí bude mít vliv na další měsíční výběr. Pokud například chcete pozastavit výběr z dubna, ujistěte se, že jste na konci března nastavili stav blokování vstupu na **zapnuto** .

Jakmile nastavíte stav blokování výběr na **zapnuto**, budou všechny výběry podrženy, dokud nepřepnete posuvník zpět na **vypnuto**. Když to uděláte, budete zahrnuti během měsíčního cyklu výběrů (za předpokladu, že byly splněny příslušné prahové hodnoty plateb). Například pokud máte vaše výběry podrženy, ale chcete mít vygenerovaný výběr v červnu, nezapomeňte přepnout stav typu blokování na **vypnuto** před koncem květen.

> [!NOTE]
> Výběr **stavu blokování** výběru se vztahuje na **všechny** zdroje příjmů placené prostřednictvím partnerského centra Microsoftu, včetně Azure Marketplace, AppSource, Microsoft Store, reklamy atd.). Pro každý zdroj výnosů nemůžete vybrat jiné stavy blokování.

### <a name="devices"></a>Zařízení

Nastavení správy zařízení se vztahuje pouze na publikování UWP. [Přečtěte si další informace](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Identifikátory GUID sledování

Globálně jedinečné identifikátory (GUID) jsou jedinečná referenční čísla (s 32 hexadecimálními číslicemi), která se dají použít ke sledování využití Azure. 

Chcete-li vytvořit identifikátory GUID pro sledování, měli byste použít generátor GUID. Tým Azure Storage vytvořil [formulář generátoru GUID](https://aka.ms/StoragePartners) , který pošle e-mailem identifikátor GUID správného formátu a bude možné ho znovu použít v různých sledovacích systémech.

Pro každou nabídku a distribuční kanál pro každý produkt doporučujeme vytvořit jedinečný identifikátor GUID. Pokud nechcete, aby vytváření sestav bylo rozděleno, můžete použít jeden identifikátor GUID pro více distribučních kanálů produktu.

Pokud nasadíte produkt pomocí šablony a je k dispozici na Azure Marketplace i na GitHubu, můžete vytvořit a zaregistrovat dva jedinečné identifikátory GUID:

- Produkt A v Azure Marketplace
- Produkt A na GitHubu

Vytváření sestav se provádí na základě hodnoty partnera (ID partnera Microsoftu) a identifikátorů GUID. Můžete také sledovat identifikátory GUID na podrobnější úrovni, které odpovídají každému plánu v rámci vaší nabídky.

Další informace najdete v tématu [sledování zákaznického využití Azure pomocí nejčastějších dotazů k identifikátorům GUID](../azure-partner-customer-usage-attribution.md#faq)).

## <a name="create-a-billing-profile"></a>Vytvořit fakturační profil

Pokud publikujete [dynamics 365 pro zákaznickou zapojení & Power Apps](./create-new-customer-engagement-offer.md) nebo [Dynamics 365 for Operations](./create-new-operations-offer.md) nabídka, musíte dokončit svůj **Fakturační profil**.

Fakturační adresa je předem vyplněná z vaší právnické osoby a tuto adresu můžete později aktualizovat. Pole DPH a ID DPH jsou volitelná.  Název země nebo oblasti a název společnosti nelze upravovat.

## <a name="multi-user-account-management"></a>Správa účtů s více uživateli

Partnerské centrum používá [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) pro přístup a správu účtu s více uživateli. Služba Azure AD vaší organizace se v rámci procesu registrace automaticky přidruží k vašemu účtu partnerského centra.

## <a name="manage-users"></a>Správa uživatelů

Část **Uživatelé** partnerského centra (v části **Nastavení účtu**) vám umožní spravovat uživatele, skupiny a aplikace Azure AD, které mají přístup k vašemu účtu partnerského centra, pomocí Azure AD. Váš účet musí mít oprávnění na [**úrovni manažera**](#define-user-roles-and-permissions) pro [pracovní účet (tenanta Azure AD)](./company-work-accounts.md) , ve kterém chcete přidat nebo upravit uživatele. Pokud chcete spravovat uživatele v rámci jiného pracovního účtu nebo tenanta, budete se muset odhlásit a pak znovu přihlásit jako uživatel s oprávněním **správce** pro tento pracovní účet nebo tenanta.

Až budete přihlášeni pomocí svého pracovního účtu (tenant Azure AD), můžete:

- [Přidat nebo odebrat uživatele](#add-existing-users)
- [Změna hesla uživatele](#change-a-user-password)
- [Přidat nebo odebrat skupiny](#manage-groups)
- [Přidání nebo odebrání aplikací Azure AD](#add-new-azure-ad-applications)
- [Správa klíčů pro aplikaci Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definování rolí a oprávnění uživatelů](#define-user-roles-and-permissions)

Mějte na paměti, že všichni uživatelé partnerského centra (včetně skupin a aplikací Azure AD) musí mít aktivní pracovní účet v [Tenantovi Azure AD](#manage-tenants) , který je přidružený k vašemu účtu partnerského centra.

### <a name="add-existing-users"></a>Přidat existující uživatele

Pokud chcete přidat uživatele k vašemu účtu partnerského centra, který už existuje v [pracovním účtu vaší společnosti (tenant Azure AD)](./company-work-accounts.md):

1. Pokračujte na **Uživatelé** (v části **Nastavení účtu**) a vyberte **Přidat uživatele**.
2. Vyberte jednoho nebo více uživatelů ze seznamu, který se zobrazí. Pomocí vyhledávacího pole můžete vyhledat konkrétní uživatele.
* Pokud vyberete více než jednoho uživatele, který chcete přidat do svého účtu partnerského centra, musíte jim přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více uživatelů s různými rolemi a oprávněními, opakujte tento postup pro každou roli nebo sadu vlastních oprávnění.
3. Až budete s volbou uživatelé hotovi, vyberte **Přidat vybrané**.
4. V části **role** určete role nebo přizpůsobená oprávnění pro vybrané uživatele.
5. Vyberte **Uložit**.

### <a name="create-new-users"></a>Vytvoření nových uživatelů

Chcete-li vytvořit nové uživatelské účty, musíte mít účet s oprávněními [**globálního správce**](../../active-directory/roles/permissions-reference.md) .

1. V nabídce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat uživatele** a pak zvolte **vytvořit nové uživatele**.
1. Zadejte jméno, příjmení a uživatelské jméno pro každého nového uživatele. 
1. Pokud chcete, aby měl nový uživatel účet globálního správce v adresáři vaší organizace, zaškrtněte políčko Označit **tohoto uživatele jako globální správce ve službě Azure AD s úplnou kontrolou nad všemi prostředky adresáře**. Uživatel tak bude mít úplný přístup ke všem funkcím pro správu v Azure AD vaší společnosti. Budou moct přidávat a spravovat uživatele v pracovním účtu vaší organizace (tenant Azure AD), ale ne v partnerském centru, pokud neudělíte účtu příslušnou roli nebo oprávnění.
1. Pokud jste zaškrtli políčko, pokud chcete, aby **Tento uživatel byl globálním správcem**, budete muset zadat **E-mail pro obnovení hesla** , aby uživatel mohl v případě potřeby obnovit heslo.
1. V části **členství ve skupině** vyberte všechny skupiny, do kterých má nový uživatel patřit.
1. V části **role** určete role nebo přizpůsobená oprávnění pro uživatele.
1. Vyberte **Uložit**.

Vytvořením nového uživatele v partnerském centru se taky vytvoří účet pro tohoto uživatele v pracovním účtu (tenant Azure AD), ke kterému jste přihlášení. Změna jména uživatele v partnerském centru provede stejné změny v pracovním účtu vaší organizace (tenant Azure AD).

### <a name="invite-new-users-by-email"></a>Pozvat nové uživatele e-mailem

Pokud chcete pozvat uživatele, kteří nejsou aktuálně součástí pracovního účtu vaší společnosti (tenant Azure AD) prostřednictvím e-mailu, musíte mít účet s oprávněními [**globálního správce**](../../active-directory/roles/permissions-reference.md) .

1. Přejít na **uživatele** (v části **Nastavení účtu**) vyberte **Přidat uživatele** a pak zvolte **pozvat uživatele e-mailem**.
2. Zadejte jednu nebo více e-mailových adres (až 10), které jsou odděleny čárkami nebo středníkem.
3. V části **role** určete role nebo přizpůsobená oprávnění pro uživatele.
4. Vyberte **Uložit**.

Uživatelé, které jste pozvali, obdrží e-mailovou pozvánku k připojení k vašemu účtu partnerského centra. V pracovním účtu (tenant Azure AD) se vytvoří nový účet uživatele Guest. Každý uživatel bude muset přijmout svou pozvánku, aby mohli získat přístup k vašemu účtu.

Pokud potřebujete znovu odeslat pozvánku, navštivte stránku **Uživatelé** , vyhledejte pozvánku v seznamu uživatelů, vyberte jejich e-mailovou adresu (nebo text, který říká *pozvání čeká na vyřízení*). Potom v dolní části stránky vyberte znovu **Odeslat pozvánku**.

> [!NOTE]
> Pokud vaše organizace používá [integraci adresářů](/previous-versions/azure/azure-services/jj573653(v=azure.100)) k synchronizaci místní adresářové služby s vaší službou Azure AD, nebudete moct vytvářet nové uživatele, skupiny nebo aplikace Azure AD v partnerském centru. Vy (nebo jiný správce v místním adresáři) ho budete muset vytvořit přímo v místním adresáři, abyste je mohli zobrazit a přidat v partnerském centru.

### <a name="remove-a-user"></a>Odebrání uživatele

Pokud chcete odebrat uživatele z pracovního účtu (tenant Azure AD), pokračujte na **Uživatelé** (v části **Nastavení účtu**), vyberte uživatele, kterého chcete odebrat, pomocí zaškrtávacího políčka ve sloupci úplně vpravo a pak z dostupných akcí zvolte **Odebrat** . Zobrazí se automaticky otevírané okno s potvrzením, že chcete vybrané uživatele odebrat.

### <a name="change-a-user-password"></a>Změna hesla uživatele

Pokud některý z uživatelů potřebuje změnit heslo, může to udělat sami, pokud jste při vytváření uživatelského účtu zadali **e-mail pro obnovení hesla** . Pomocí následujících kroků můžete také aktualizovat heslo uživatele. Pokud chcete změnit heslo uživatele v pracovním účtu vaší společnosti (tenant Azure AD), musíte být přihlášeni pomocí účtu s oprávněními [**globálního správce**](../../active-directory/roles/permissions-reference.md) . Tím se změní heslo uživatele v tenantovi Azure AD spolu s heslem, které používají pro přístup k partnerskému centru.

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte název uživatelského účtu, který chcete upravit.
2. V dolní části stránky vyberte tlačítko **resetovat heslo** .
3. Zobrazí se stránka s potvrzením, na které se zobrazí přihlašovací informace pro uživatele, včetně dočasného hesla. Nezapomeňte vytisknout nebo zkopírovat tyto informace a poskytnout ji uživateli, protože po opuštění této stránky nebudete mít přístup k dočasnému heslu.

## <a name="manage-groups"></a>Správa skupin

Skupiny umožňují řídit více rolí uživatelů a oprávnění dohromady.

### <a name="add-an-existing-group"></a>Přidat existující skupinu

Pokud chcete přidat skupinu, která už existuje v pracovním účtu vaší organizace (tenant Azure AD) k vašemu účtu partnerského centra:

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat skupiny**.
2. Vyberte jednu nebo více skupin ze seznamu, který se zobrazí. Pomocí vyhledávacího pole můžete vyhledat konkrétní skupiny.
Pokud vyberete více než jednu skupinu, kterou chcete přidat do svého účtu partnerského centra, musíte jim přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více skupin s různými rolemi a oprávněními, opakujte tento postup pro každou roli nebo sadu vlastních oprávnění.
3. Až budete hotovi s volbou možnosti skupiny, vyberte **Přidat vybrané**.
4. V části **role** určete role nebo přizpůsobená oprávnění pro vybrané skupiny. Všichni členové skupiny budou mít přístup k vašemu účtu partnerského centra s oprávněními, která použijete pro skupinu, bez ohledu na role a oprávnění přidružená ke svému individuálnímu účtu.
5. Vyberte **Uložit**.

Když přidáte existující skupinu, bude mít každý uživatel, který je členem této skupiny, přístup k vašemu účtu partnerského centra s oprávněními přidruženými k přiřazené roli skupiny.

### <a name="add-a-new-group"></a>Přidání nové skupiny

Přidání značky – nová skupina k vašemu účtu partnerského centra:

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat skupiny**.
2. Na další stránce vyberte možnost **Nová skupina**.
3. Zadejte zobrazovaný název nové skupiny.
4. Určete role nebo přizpůsobená oprávnění pro skupinu. Všichni členové skupiny budou mít přístup k vašemu účtu partnerského centra s oprávněními, která použijete, a to bez ohledu na role/oprávnění přidružené k jejich jednotlivému účtu.
5. V seznamu, který se zobrazí, vyberte uživatele (y) pro novou skupinu. Pomocí vyhledávacího pole můžete vyhledat konkrétní uživatele.
6. Až budete s výběrem možnosti uživatelé hotovi, vyberte **Přidat vybrané** a přidejte je do nové skupiny.
7. Vyberte **Uložit**.

Tato nová skupina se vytvoří i v pracovním účtu vaší organizace (tenant Azure AD), a ne jenom v účtu partnerského centra.

### <a name="remove-a-group"></a>Odebrat skupinu

Pokud chcete odebrat skupinu z pracovního účtu (tenant Azure AD), pokračujte na **Uživatelé** (v části **Nastavení účtu**), vyberte skupinu, kterou chcete odebrat, pomocí zaškrtávacího políčka ve sloupci úplně vpravo a pak zvolte **Odebrat** z dostupných akcí. Zobrazí se automaticky otevírané okno s potvrzením, že chcete odebrat vybrané skupiny.

## <a name="manage-azure-ad-applications"></a>Správa aplikací Azure AD

Aplikacím nebo službám, které jsou součástí Azure AD vaší společnosti, můžete dovolit přístup k účtu partnerského centra.

### <a name="add-existing-azure-ad-applications"></a>Přidat existující aplikace služby Azure AD

Chcete-li přidat aplikace, které již existují v Azure Active Directory vaší společnosti:

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat aplikace Azure AD**.
2. V seznamu, který se zobrazí, vyberte jednu nebo víc aplikací Azure AD. Pomocí vyhledávacího pole můžete vyhledat konkrétní aplikace služby Azure AD. Pokud vyberete více než jednu aplikaci Azure AD, kterou chcete přidat do svého účtu partnerského centra, musíte jim přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více aplikací služby Azure AD s různými rolemi a oprávněními, opakujte tento postup pro každou roli nebo sadu vlastních oprávnění.
3. Až budete hotovi s výběrem aplikací Azure AD, vyberte **Přidat vybrané**.
4. V části **role** určete role nebo vlastní oprávnění pro vybrané aplikace služby Azure AD.
5. Vyberte **Uložit**.

### <a name="add-new-azure-ad-applications"></a>Přidat nové aplikace Azure AD

Pokud chcete partnerskému centru udělit přístup k novému účtu aplikace služby Azure AD, můžete ho vytvořit v části **Uživatelé** . Tím se vytvoří nový účet v pracovním účtu vaší společnosti (tenant Azure AD), nikoli jenom v účtu partnerského centra. Pokud tuto aplikaci Azure AD používáte primárně pro ověřování partnerského centra a nepotřebujete, aby k nim uživatelé měli přístup přímo, můžete zadat libovolnou platnou adresu **adresy URL odpovědi** a **identifikátoru URI ID aplikace**, pokud tyto hodnoty nepoužívá žádná jiná aplikace služby Azure AD ve vašem adresáři.

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat aplikace Azure AD**.
2. Na další stránce vyberte **Nová aplikace Azure AD**.
3. Zadejte **adresu URL odpovědi** pro novou aplikaci Azure AD. Toto je adresa URL, kde se uživatelé můžou přihlásit a používat vaši aplikaci Azure AD (někdy taky označovanou jako adresa URL aplikace nebo adresa Sign-On URL). **Adresa URL odpovědi** nemůže být delší než 256 znaků a musí být v rámci vašeho adresáře jedinečná.
4. Zadejte **identifikátor URI ID aplikace** pro novou aplikaci Azure AD. Toto je logický identifikátor aplikace Azure AD, který se zobrazí při odeslání žádosti o jednotné přihlašování do Azure AD. **Identifikátor URI ID aplikace** musí být pro každou aplikaci Azure AD ve vašem adresáři jedinečný. Toto ID nemůže být delší než 256 znaků. Další informace o identifikátoru URI ID aplikace najdete v tématu [integrování aplikací pomocí Azure Active Directory](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)).
5. V části **role** určete role nebo přizpůsobená oprávnění pro aplikaci Azure AD.
6. Vyberte **Uložit**.

Po přidání nebo vytvoření aplikace Azure AD se můžete vrátit do části **Uživatelé** a vybrat název aplikace pro kontrolu nastavení aplikace, včetně ID TENANTA, ID klienta, adresy URL odpovědi a identifikátoru URI ID aplikace.

### <a name="remove-an-azure-ad-application"></a>Odebrání aplikace Azure AD

Pokud chcete odebrat aplikaci z pracovního účtu (tenant Azure AD), pokračujte na **Uživatelé** (v části **Nastavení účtu**), vyberte aplikaci, kterou chcete odebrat, pomocí zaškrtávacího políčka ve sloupci úplně vpravo a pak z dostupných akcí zvolte **Odebrat** . Zobrazí se automaticky otevírané okno s potvrzením, že chcete vybrané aplikace odebrat.

### <a name="manage-keys-for-an-azure-ad-application"></a>Správa klíčů pro aplikaci Azure AD

Pokud vaše aplikace Azure AD čte a zapisuje data v Microsoft Azure AD, bude potřebovat klíč. Klíče pro aplikaci Azure AD můžete vytvořit úpravou informací v partnerském centru. Můžete také odebrat klíče, které už nepotřebujete.

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte název aplikace Azure AD. Zobrazí se všechny aktivní klíče pro aplikaci Azure AD, včetně data, ve kterém byl klíč vytvořen, a jeho vypršení platnosti 50.
2. Pokud chcete odebrat klíč, který už nepotřebujete, vyberte **Odebrat**.
3. Chcete-li přidat nový klíč, vyberte možnost **Přidat nový klíč**.
4. Zobrazí se obrazovka s **ID klienta** a **klíčovými hodnotami**. Nezapomeňte tyto informace vytisknout nebo zkopírovat, protože po opuštění této stránky nebudete moct znovu získat přístup.
5. Pokud chcete vytvořit více klíčů, vyberte **Přidat další klíč**.

## <a name="define-user-roles-and-permissions"></a>Definování rolí a oprávnění uživatelů

Uživatelům vaší společnosti můžete přiřadit následující role a oprávnění pro program komerčního tržiště v partnerském centru:

- **Vlastník**
  - "Je to osoba, která poprvé vytvořila účet a má k němu úplný přístup, včetně možnosti vytvářet a upravovat všechny uživatele účtu a měnit všechna finanční a účetní nastavení. Účet má obvykle pouze jednoho vlastníka. "
- **Manažer**
  - Má přístup ke všem funkcím účet Microsoft s výjimkou nastavení daně a výběr
  - Může spravovat uživatele, role a pracovní účty (klienty).
- **Vývojář**
  - Může spravovat a publikovat nabídky.
  - Může zobrazit některé sestavy vydavatelů.

> [!NOTE]
> V případě programu komerčního obchodu se nepoužívají globální správce, obchodní přispěvatel, finanční přispěvatel a role na trhu. Přiřazení těchto rolí uživatelům se nijak neprojeví. Oprávnění uživatelům přidělují jenom role správce a vývojář.

Další informace o správě rolí a oprávnění v jiných oblastech partnerského centra, jako je Azure Active Directory (AD), zprostředkovatel Cloud Solution Provider (CSP), ovládací panel dodavatel (CPV), uživatelé typu Host nebo Microsoft Partner Network (MPN), najdete v tématu [přiřazení rolí uživatelů a oprávnění v partnerském centru](/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Správa tenantů

Tenant Azure Active Directory (AD), označovaný také jako "pracovní účet" v celé této dokumentaci, je reprezentace vaší organizace nastavená v Azure Portal a pomůže vám spravovat konkrétní instanci cloudových služeb Microsoftu pro interní a externí uživatele. Pokud se vaše organizace přihlásila k odběru cloudové služby Microsoftu, jako je Azure, Microsoft Intune nebo Microsoft 365, vytvořil se tenant Azure AD za vás.

Můžete nastavit více tenantů pro použití s partnerským centrem. Každý uživatel s rolí **správce** v účtu partnerského centra bude mít možnost Přidat a odebrat klienty služby Azure AD z tohoto účtu.  

### <a name="add-an-existing-tenant"></a>Přidat existujícího tenanta

K přidružení jiného tenanta Azure AD k vašemu účtu partnerského centra:

1. Na stránce **klienti** (v části **Nastavení účtu**) vyberte **přidružit jiného tenanta Azure AD**.
2. Zadejte svoje přihlašovací údaje Azure AD pro tenanta, kterého chcete přidružit.
3. Zkontrolujte název organizace a domény pro vašeho tenanta Azure AD. Přidružení dokončíte výběrem **Potvrdit**.

Pokud je přidružení úspěšné, budete připraveni přidat a spravovat uživatele účtu v části **Uživatelé** v partnerském centru.

### <a name="create-a-new-tenant"></a>Vytvoření nového tenanta

Pokud chcete vytvořit značku nového tenanta Azure AD s vaším účtem partnerského centra:

1. Na stránce **klienti** (v části **Nastavení účtu**) vyberte **vytvořit nového tenanta Azure AD**.
2. Zadejte informace o adresáři pro novou službu Azure AD:
    - **Název domény**: jedinečný název, který budeme používat pro vaši doménu Azure AD, společně s ". onmicrosoft.com". Pokud jste například zadali "example", vaše doména Azure AD by byla "example.onmicrosoft.com".
    - **Kontaktní e-mail**: e-mailová adresa, na které vám můžeme v případě potřeby kontaktovat váš účet.
    - **Informace o uživatelském účtu globálního správce**: jméno, příjmení, uživatelské jméno a heslo, které chcete použít pro nový účet globálního správce.
3. Vyberte **vytvořit** a potvrďte informace o nové doméně a účtu.
4. Přihlaste se pomocí svého nového uživatelského jména a hesla globálního správce služby Azure AD, abyste mohli začít [přidávat a spravovat uživatele](#manage-users).

Další informace o vytváření nových klientů v rámci Azure Portal a nikoli prostřednictvím portálu pro Partnerský portál najdete v článku [Vytvoření nového tenanta v Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

### <a name="remove-a-tenant"></a>Odebrání tenanta

Pokud chcete odebrat tenanta z účtu partnerského centra, Najděte jeho název na stránce **tenantů** (v **Nastavení účtu**) a pak vyberte **Odebrat**. Zobrazí se výzva k potvrzení, že chcete klienta odebrat. Až to uděláte, žádní uživatelé v tomto tenantovi se nebudou moct přihlašovat k účtu partnerského centra a všechna oprávnění, která jste nakonfigurovali pro tyto uživatele, se odeberou.

Když odeberete tenanta, všichni uživatelé, kteří byli přidáni do účtu partnerského centra z tohoto tenanta, se už nebudou moct přihlašovat k účtu.

> [!TIP]
> Tenanta nemůžete odebrat, pokud jste aktuálně přihlášení k partnerskému centru pomocí účtu ve stejném tenantovi. Pokud chcete klienta odebrat, musíte se přihlásit do partnerského centra jako **správce** pro jiného tenanta, který je k tomuto účtu přidružený. Pokud se k účtu přidruží jenom jeden tenant, dá se tento tenant odebrat jenom po přihlášení pomocí účet Microsoft, který účet otevřel.

## <a name="agreements"></a>Smlouvy

Část **smlouvy** partnerského centra (v části **Nastavení účtu**) umožňuje zobrazit seznam smluv publikování, které jste povolili. Tyto smlouvy jsou uvedené v souladu s názvem a číslem verze včetně data přijetí a jména uživatele, který smlouvu přijal.

Pokud jsou k dispozici aktualizace smluv, které vyžadují vaši pozornost, můžou se v horní části této stránky zobrazit **potřebné akce** . Pokud chcete přijmout aktualizovanou smlouvu, nejdřív si přečtěte verzi odkazované smlouvy a pak vyberte **přijmout smlouvu**.
