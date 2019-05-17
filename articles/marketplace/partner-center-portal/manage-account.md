---
title: Jak spravovat obchodní Marketplace účet v partnerském centru
description: Další informace o správě obchodní Marketplace účet v partnerském centru.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: 935d2e1c96705506636c2883113a64bb70c39336
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806188"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Jak spravovat vaše obchodní Marketplace účet v partnerském centru 

Jakmile [vytvořili účet v partnerském centru](./create-account.md), můžete spravovat svůj účet a pomocí nabídky [řídicí panel komerční Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

V tomto článku začneme budete zabývat jak spravovat váš účet v partnerském centru, také se naučíte: 

- [Přístup k nastavení účtu partnerského centra](#access-your-account-settings)
- [Najít ID vydavatele, ID prodejce, ID uživatele a klienty Azure AD](#account-details)
- [Aktualizovat kontaktní informace](#contact-info)
- [Správa finanční údaje (Výběr účtu, daň profilu, stav blokování výběr)](#financial-details)
- [Nastavení sledování identifikátory GUID pro sledování využití ze strany zákazníků](#tracking-guids)
- [Správce uživatelů](#manage-users)
- [Správce skupiny](#manage-groups)
- [Aplikace Správce Azure AD](#manage-azure-ad-applications)
- [Definování rolí uživatelů a oprávnění](#define-user-roles-and-permissions)
- [Správa tenantů Azure AD (pracovní účty)](#manage-tenants)
- [Správce partnerského centra smlouvy](#agreements)


## <a name="access-your-account-settings"></a>Přístup k nastavení účtu

Pokud jste tak již neučinili, vy (nebo správce ve vaší organizaci) by měl přístup [nastavení účtu](https://partner.microsoft.com/dashboard/account/management) pro váš účet v partnerském centru za účelem:
- Zkontrolujte stav ověření účtu vaší společnosti
- Potvrďte ID prodejce, MPN ID, ID vydavatele a obraťte se na informace, včetně kontakt společnosti schvalovatele a prodejce
- nastavení finanční údaje vaší společnosti, včetně osvobození od daně v případě potřeby
- Vytvořte uživatelské účty pro každého, kdo bude používat obchodní účet v partnerském centru

### <a name="open-developer-settings"></a>Otevřete nastavení pro vývojáře

Nastavení účtu se nachází v pravém horním rohu vašeho [řídicí panel komerční Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace) v partnerském centru. Vyberte ikonu ozubeného kola (v pravém horním rohu řídicího panelu) a pak vyberte **nastavení pro vývojáře**. 

![Nabídka nastavení účet v partnerském centru](./media/dashboard-developer-settings.png)

Uvnitř **nastavení účtu**, bude moct zobrazit vaše:
- **Podrobnosti účtu**: Typ účtu a stav účtu
- **ID vydavatele**: ID prodejce, ID uživatele, ID vydavatele, tenantů Azure AD, atd.
- **Obraťte se na informace o**: Zobrazovaný název vydavatele, jméno kontaktní osoby prodejce, e-mailu, Telefon a adresa
- **Finanční údaje**: Výběr účtu, daň profilu a výběr stav blokování
- **zařízení**: Žádná testovací zařízení spojené s vaším účtem
- **Sledování identifikátory GUID**: Žádné sledování identifikátory GUID spojený s vaším účtem

### <a name="account-details"></a>Podrobnosti o účtu

V části Podrobnosti účtu můžete zobrazit základní informace, jako je vaše **typ účtu** (společnosti nebo osoby) a **stav ověření** vašeho účtu. Během procesu ověření vašeho účtu tato nastavení se zobrazí každý krok vyžaduje, včetně ověření e-mailu, ověření zaměstnání a obchodních ověření. Můžete taky aktualizovat e-mailu a znovu odeslat ověření v případě potřeby. 

### <a name="publisher-ids"></a>ID vydavatele

V části ID vydavatele se zobrazí vaše **prodejce ID**, **MPN ID**, a **ID vydavatele**. Tyto hodnoty jsou přiřazené společností Microsoft k jednoznačné identifikaci vašeho účtu vývojáře webu a nelze jej upravit.

### <a name="contact-info"></a>Kontaktní údaje

V části informace o kontaktu se zobrazí vaše **zobrazovaný název vydavatele**, **kontaktní údaje prodejce** (jméno kontaktu, e-mailu, telefonní číslo a adresu pro prodejce společnosti) a **společnosti Schvalovatel** (název, e-mailu a telefonní číslo uživatele s oprávnění ke schválení při rozhodování o společnosti). 

### <a name="financial-details"></a>Finanční údaje

V části finanční údaje můžete zadat nebo aktualizovat vaše finanční informace, pokud publikujete placené aplikace, doplňky nebo služby. 

Pokud chcete seznam bezplatných nabídek, není nutné nastavit účet výběr nebo vyplňovat jakékoli daně. Pokud později změníte své rozhodnutí a rozhodnete, že je chtějí prodávat prostřednictvím společnosti Microsoft, můžete nastavit při vytváření svého účtu výběr a vyplňovat daně v daném čase. 

#### <a name="payout-account"></a>Výběr účtu

Výběr účtu se bankovním účtu, ke kterému se bude pokračovat odesílají z prodeje. Tento účet bank musí být ve stejné zemi, kde jste zaregistrovali účet v partnerském centru.

Chcete-li nastavení vašeho účtu pro výběr, je potřeba **přidružit Account Microsoft**:
1. V **nastavení účtu**v části **finanční údaje** vyberte **přidružit Account Microsoft**. 
2. Po zobrazení výzvy, přihlaste se pomocí vašeho účtu Microsoft (MSA). Tento účet již nelze přidružit jiný účet v partnerském centru. 
3. Dokončete nastavení účtu výběr protokolu zcela z partnerského centra, pak se přihlaste zpět pomocí Account Microsoft (nikoli svého pracovního účtu). 

Teď, když Account Microsoft souvisí, chcete-li přidat výběr účtu, budete muset:
- **Vyberte způsob platby**: Bankovním účtu nebo PayPal
- **Přidat platební údaje**: To může zahrnovat výběr typu účtu (Kontrola nebo úspory), zadáte jméno vlastníka účtu, číslo účtu a směrování číslo, fakturační adresu, telefonní číslo a PayPal e-mailovou adresu. * Další informace o používání služby PayPal jako způsob platby vašeho účtu a chcete zjistit, zda je podporován ve vaší oblasti na trhu, naleznete v tématu [PayPal informace](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Změna účtu výběr zpozdit vaší platby podle až jeden cyklus platby. Toto zpoždění dochází, protože budeme potřebovat ověřit změnu účtu stejně jako při prvním nastavování účtu výběr. Budete stále platby za celou částku po váš účet byl ověřen; všechny platby z důvodu pro aktuální platební cyklu přibudou další.  

#### <a name="tax-profile"></a>Daň za profilu

Zkontrolujte aktuální stav profilu daně, potvrzení správné **typ Entity** a **certifikát daních** se zobrazí. Vyberte **upravit** aktualizovat nebo dokončete všechny nutné formuláře.

Aby bylo možné navázat stavu daně, je nutné zadat zemi vašeho pobytu a citizenship a proveďte příslušné daně formuláře přidružené k vaší země/oblasti.

Bez ohledu na zemi vašeho pobytu nebo citizenship je nutné vyplnit USA daně formulářů k prodeji všechny nabídky Microsoftu. Partneři, kteří splňují určité požadavky na rezidenci Spojených států musí vyplňte formulář IRS W-9. Dalšími partnery mimo území Spojených států musí vyplňte formulář IRS W-8. Můžete vyplnit tyto formy online jak vyplnit profil pro daně.

Spojené státy jednotlivé daňové identifikační číslo (nebo ITIN) se nevyžaduje pro příjem platby od Microsoftu nebo chcete uplatnit nárok na výhody smlouvy daně.

Můžete vyplňte a odešlete vaše formuláře daně elektronicky v partnerském centru; ve většině případů není nutné tisku a e-mailů formulářů.

Různých zemí a oblastí mají různé daně požadavky. Přesná, musí platit daně závisí na jiných zemí a oblastí, kde prodej vaší nabídky. Microsoft vrátí prodeje a daň za použití vaším jménem v některých zemích. Těchto zemí se bude považovat právě výpis vaší nabídky. V jiných zemích, v závislosti na tom, kde jsou registrovány budete muset hradit daně z prodeje a použít daně pro prodeje přímo do místní daňovému úřadu. Kromě toho může být prodejních výnosů, který jste dostali dani jako výnosy. Důrazně doporučujeme pro vaši zemi nebo oblast, která nejlépe vám pomohou určit informace o správné daň za prodejní transakci Microsoft kontaktovat příslušné oprávnění.

##### <a name="withholding-rates"></a>Odmítnutí sazby
Informace, které uvedete ve vašich formulářích daně určuje srážkové míra příslušné daně. Srážkové sazba se vztahuje pouze na prodej, které jste provedli v USA. prodeje do umístění mimo USA se nevztahují odmítnutí. Srážkové sazby se liší, ale pro většinu vývojářů registrace mimo území Spojených států, výchozí míra je 30 %. Máte možnost snížení tato sazba, pokud vaše země má daně z příjmu smlouvy souhlasím s nimi Spojených státech amerických.

##### <a name="tax-treaty-benefits"></a>Výhody smlouvy daně
Pokud jste mimo území Spojených států, je možné využít výhod daně výhody smlouvy. Mezi tyto výhody zemi se liší a vám umožní snížit množství daně, které se sráží Microsoft. Po dokončení část II formulář W-8BEN můžete převzít daňové výhody smlouvy. Doporučujeme, abyste měli komunikovat s příslušnými prostředky ve vaší zemi či oblasti, kterou chcete určit, zda je na vás vztahovat tyto výhody.

[Další informace o podrobnosti daně pro vývojáře Windows aplikace a hry a vydavatelům Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Výběr stavu blokování

Ve výchozím nastavení Microsoft pošle plateb každý měsíc. Máte ale možnost pozastavit vaše výplaty, čímž zabráníte odesílání platby ke svému účtu. Pokud budete chtít pozastavit vaše výplaty, budeme nadále záznamu všechny výnosů, který můžete získat a zadat podrobnosti uvedené v vaše **výběr Souhrn**. Ke svému účtu, nebudeme vám posílat žádné platby dokud neodeberete blokování. 

Pozdržení vaší platby, přejděte na **nastavení účtu**. V části **finanční údaje**v **stav blokování výběr** části, přepnout jezdce **na**. Můžete kdykoli změnit váš výběr stav blokování, ale mějte na paměti, že vaše rozhodnutí bude mít dopad na další měsíční výběr. Například pokud chcete pro výběr v dubnu, nezapomeňte nastavit stav blokování výběr **na** do konce dne.

Jakmile nastavíte váš výběr uložení stavu **na**, všechny výplaty bude blokováno, dokud při přepnutí posuvníku zpět **vypnout**. Pokud tak učiníte, bude součástí během příštího cyklu měsíční výběr (za předpokladu, že jsou splněné všechny použitelné platební prahové hodnoty). Například pokud už nějakou vaše výplaty pozastavené, ale by měl mít výběr vygenerované v červnu, ujistěte se, že přepnete výběr uložení stavu **vypnout** do konce května.

> [!NOTE]
> Vaše **stav blokování výběr** výběr platí pro **všechny** zdroje příjmů, zaplacených prostřednictvím Microsoft Partner Center, včetně webu Azure Marketplace AppSource, Microsoft Store, reklamy, atd.). Nelze vybrat jiný blokování stavy pro jednotlivé zdroje příjmů.

### <a name="devices"></a>Zařízení

Nastavení správy zařízení platí jenom pro UPW publikování. [Další informace](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Sledování identifikátory GUID

Globálně jedinečné identifikátory (GUID) jsou čísla jedinečný odkaz (s 32 šestnáctkových číslic), která lze použít pro sledování využití Azure. 

Vytvořit GUID pro sledování, měli by používat GUID generator. Vytvořil tým Azure Storage [formátu identifikátoru GUID generátor](https://aka.ms/StoragePartners) , který bude e-mailem GUID ve správném formátu a lze opětovně použít napříč systémy různých sledování.

Doporučujeme že vytvořit jedinečný identifikátor GUID pro každou nabídku a distribuční kanál pro jednotlivé produkty. Můžete se rozhodnout použít jeden identifikátor GUID pro více distribučních kanálů produktu, pokud nechcete, aby vytváření sestav, který se má rozdělit.

Pokud nasadíte produktu pomocí šablony a je k dispozici na Azure Marketplace a na Githubu, můžete vytvořit a zaregistrovat 2 odlišné identifikátory GUID:

*   Produkt A na webu Azure Marketplace
*   Produkt A na Githubu

Vytváření sestav se provádí tak, že hodnota partnera (ID partnera společnosti Microsoft) a identifikátory GUID. Můžete také sledovat identifikátory GUID na podrobnější úrovni, takže každý plán v rámci vaší nabídky.

Další informace najdete v tématu [využití ze strany zákazníků sledování Azure s nejčastější dotazy týkající se identifikátory GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Správa více uživatelských účtů

Partnerské centrum využívá [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) pro přístup k více uživatelských účtů a správu. Vaše organizace Azure AD se automaticky přidruží účet v partnerském centru jako součást procesu registrace. 

## <a name="manage-users"></a>Spravovat uživatele

**Uživatelé** část partnerského centra (v části **nastavení účtu**) umožňuje spravovat uživatele, skupiny a aplikace Azure AD, které mají přístup k vašemu účtu partnerského centra pomocí služby Azure AD. Všimněte si, že pokud chcete spravovat uživatele, musíte být přihlášeni pomocí vaší [pracovní účet](./company-work-accounts.md) (související služby Azure AD tenant). Ke správě uživatelů v rámci různých pracovního účtu nebo tenantovi, budete muset odhlásit a pak se přihlásit jako uživatel s **správce** oprávnění, která pracovní účet / tenanta. 

Jakmile se přihlásíte pomocí svého pracovního účtu (tenanta Azure AD), můžete:
- [Přidání nebo odebrání uživatelů](#add-or-remove-users)
- [Změní heslo uživatele](#change-a-user-password)
- [Přidat nebo odebrat skupiny](#add-or-remove-users)
- [Přidat nebo odebrat aplikace služby Azure AD](#add-new-azure-ad-applications)
- [Spravovat klíče pro aplikaci Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definování rolí uživatelů a oprávnění](#define-user-roles-and-permissions)


Pamatujte, že všichni uživatelé partnerského centra (včetně skupin a aplikací Azure AD) musí mít aktivní pracovní účet do [tenanta Azure AD](#manage-tenants) , která je přiřazená k vašemu účtu partnerského centra. 

### <a name="add-or-remove-users"></a>Přidání nebo odebrání uživatelů

Váš účet musí mít [ **úroveň Manager** ](#define-user-roles-and-permissions) oprávnění pro [pracovní účet (Azure AD tenant)](./company-work-accounts.md) ve které chcete přidat nebo upravit uživatele.

#### <a name="add-existing-users"></a>Přidat existující uživatele

Chcete-li přidat uživatele na váš účet v partnerském centru, které již existují ve vaší společnosti [pracovní účet (Azure AD tenant)](./company-work-accounts.md):

1. Přejděte na **uživatelé** (v části **nastavení účtu**) a vyberte **přidat uživatele**.
2. Vyberte jeden nebo více uživatelů ze seznamu, který se zobrazí. Do vyhledávacího pole můžete vyhledat konkrétní uživatele.
* Pokud vyberete více než jeden uživatel přidat váš účet v partnerském centru, je nutné jim přiřadit stejné roli nebo sadu vlastních oprávnění. Chcete-li přidat více uživatelů s jinou rolí nebo oprávnění, tento postup opakujte pro každou roli nebo sadu vlastních oprávnění.
3.  Po výběru uživatele klikněte na **přidat vybrané**.
5.  V **role** nastavte role nebo vlastních oprávnění pro vybrané uživatele.
6.  Vyberte **Uložit**.

#### <a name="create-new-users"></a>Vytvoření nových uživatelů

Vytvoření zcela nové uživatelské účty, musíte mít účet s [ **globálního správce** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) oprávnění. 

1. Přejděte na **uživatelé** (v části **nastavení účtu**) vyberte **přidat uživatele**, klikněte na tlačítko **vytvořit nové uživatele**.
1. Zadejte jméno, příjmení a uživatelské jméno pro každého nového uživatele. 
1. Pokud chcete, aby nového uživatele pro účet globálního správce v adresáři vaší organizace, zaškrtněte políčko **nastavení tohoto uživatele jako globální správce ve službě Azure AD s úplnou kontrolou nad všech prostředků adresáře**. Získáte tak úplný přístup uživatelů ke všem funkcím pro správu ve vaší společnosti v Azure AD. Budou moct přidávat a spravovat uživatele ve vaší organizaci pracovní účet (tenanta Azure AD), i když nejsou v Partner Center, pokud udělíte účtu příslušné role nebo oprávnění. 
1. Pokud je zaškrtnuto políčko **nastavení tohoto uživatele jako globální správce**, budete muset zadat **e-mailu pro obnovení hesla** pro uživatele v případě potřeby obnovit heslo.
1. V **členství ve skupině** vyberte všechny skupiny, do kterých chcete nový uživatel patří.
1. V **role** nastavte role nebo vlastních oprávnění pro uživatele.
1. Vyberte **Uložit**.

Vytváří se nový uživatel v partnerském centru také vytvořte účet pro tohoto uživatele použije pracovní účet (tenanta Azure AD), ke kterému jste přihlášeni. Provádění změn uživatelské jméno v partnerském centru provést stejné změny ve vaší organizaci pracovní účet (tenanta Azure AD).

#### <a name="invite-new-users-by-email"></a>Pozvat nového uživatele e-mailem

Pozvat uživatele, kteří aktuálně nejsou součástí vaší společnosti pracovní účet (Azure AD tenant) e-mailem, musí mít účet s [ **globálního správce** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) oprávnění. 

1. Přejděte na **uživatelé** (v části **nastavení účtu**) vyberte **přidat uživatele**, klikněte na tlačítko **pozvat uživatele e-mailem**.
2. Zadejte jeden nebo více e-mailové adresy (maximálně deset), oddělených čárkami nebo středníkem.
3. V **role** nastavte role nebo vlastních oprávnění pro uživatele.
4. Vyberte **Uložit**.

Pozvaní uživatelé získá e-mailovou pozvánku pro váš účet v partnerském centru připojení. Vytvoří se nový uživatel typu Host účet do svého pracovního účtu (tenanta Azure AD). Každý uživatel bude muset přijmout pozvánku nepřijal před přístupem k účtu.

Pokud je potřeba znovu odeslat pozvánku, najdete **uživatelé** stránce, v seznamu uživatelů najít e-mailové pozvánce, vyberte e-mailová adresa (nebo text, který říká *Pozvánka čeká*). Potom v dolní části stránky vyberte **znovu odeslat pozvánku**.
 

> [!NOTE]
> Pokud vaše organizace používá [integrace adresáře](https://go.microsoft.com/fwlink/p/?LinkID=724033) synchronizovat místní adresářové služby Azure AD, nebudete moct vytvořit nové uživatele, skupiny nebo aplikace Azure AD v partnerském centru. Vy (nebo jiný správce ve vaší místní adresář), bude nutné vytvořit předtím, než budete moct zobrazit a přidat je v partnerském centru přímo v místním adresáři.

#### <a name="remove-a-user"></a>Odebrání uživatele

Odebrání uživatele ze svého pracovního účtu (tenanta Azure AD), přejděte na **uživatelé** (v části **nastavení účtu**), vyberte uživatele, který chcete odebrat, pomocí zaškrtávacího políčka ve sloupci úplně vpravo klikněte na tlačítko  **Odebrat** z dostupných akcí. K potvrzení, že chcete odebrat vybrané uživatele se zobrazí automaticky otevírané okno.

#### <a name="change-a-user-password"></a>Změní heslo uživatele

Pokud jeden z vašich uživatelů je potřeba změnit své heslo, dělají to sami, pokud jste zadali **e-mailu pro obnovení hesla** při vytváření uživatelského účtu. Pomocí následujících kroků můžete také aktualizovat hesla uživatele. Chcete-li změnit heslo uživatele ve vaší společnosti pracovní účet (tenanta Azure AD), musíte být přihlášeni na účet s [ **globálního správce** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) oprávnění. Všimněte si, že tato akce změní heslo uživatele ve vašem tenantovi Azure AD, spolu s heslem se používají pro přístup k partnerskému centru.

1.  Z **uživatelé** stránky (v části **nastavení účtu**), vyberte název uživatelského účtu, který chcete upravit.
2.  Vyberte **resetovat heslo** tlačítko v dolní části stránky.
3.  Stránka s potvrzením budou zobrazeny přihlašovací informace pro uživatele, včetně dočasné heslo. Nezapomeňte si vytisknout nebo zkopírujte tyto údaje a zadejte pro uživatele, nebudou mít přístup k dočasné heslo po opuštění této stránky.


## <a name="manage-groups"></a>Spravovat skupiny

Skupiny umožňují řídit více uživatelských rolí a oprávnění všechno dohromady.

#### <a name="add-an-existing-group"></a>Přidat existující skupinu

Přidat že skupinu, která již existuje ve vaší organizaci pracovní účet (tenanta Azure AD) pro váš účet v partnerském centru: 

1.  Z **uživatelé** stránky (v části **nastavení účtu**) vyberte **přidat skupiny**.
2.  Vyberte jednu nebo více skupin ze seznamu, který se zobrazí. Do vyhledávacího pole můžete použít k vyhledání konkrétních skupin.
Pokud vyberete více než jedné skupině přidat účet v partnerském centru, je nutné jim přiřadit stejné roli nebo sadu vlastních oprávnění. Chcete-li přidat více skupinám s jinou rolí nebo oprávnění, tento postup opakujte pro každou roli nebo sadu vlastních oprávnění.
3.  Po dokončení výběru skupiny, klikněte na tlačítko **přidat vybrané**.
4.  V **role** nastavte role nebo vlastních oprávnění pro vybrané skupiny. Všichni členové skupiny budou mít přístup ke svému účtu partnerského centra s oprávněními, že použijete ke skupině, bez ohledu na to, role a oprávnění pomocí svého individuálního účtu.
5.  Vyberte **Uložit**.

Když přidáte existující skupiny, každý uživatel, který je členem této skupiny bude mít přístup k účtu partnerského centra s oprávnění spojená s přiřazenou roli skupiny.

#### <a name="add-a-new-group"></a>Přidat novou skupinu

Chcete-li přidat skupinu úplně na váš účet v partnerském centru: 

1.  Z **uživatelé** stránky (v části **nastavení účtu**) vyberte **přidat skupiny**.
2.  Na další stránce vyberte **novou skupinu**.
3.  Zadejte zobrazovaný název pro novou skupinu.
4.  Zadejte role nebo vlastních oprávnění pro skupinu. Všichni členové skupiny budou mít přístup ke svému účtu partnerského centra s oprávněními, že použijete, bez ohledu na to, role nebo oprávnění přidružené pomocí svého individuálního účtu.
5.  Vyberte uživatele pro novou skupinu ze seznamu, který se zobrazí. Do vyhledávacího pole můžete vyhledat konkrétní uživatele.
6.  Až budete hotovi, vyberte uživatele, klikněte na **přidat vybrané** je přidat do nové skupiny.
7.  Vyberte **Uložit**.

Všimněte si, že tato nová skupina vytvoří se pracovní účet vaší organizace (tenanta Azure AD), nejen ve vašem účtu partnerského centra.

#### <a name="remove-a-group"></a>Odebrat skupinu

K odebrání skupiny ze svého pracovního účtu (tenanta Azure AD), přejděte na **uživatelé** (v části **nastavení účtu**), vyberte skupinu, která byste chtěli odebrat pomocí zaškrtávacího políčka ve sloupci úplně vpravo klikněte na tlačítko  **Odebrat** z dostupných akcí. K potvrzení, že chcete odstranit vybrané skupiny se zobrazí automaticky otevírané okno.

## <a name="manage-azure-ad-applications"></a>Správa aplikací Azure AD

Můžete povolit aplikacím nebo službám, které jsou součástí vaší společnosti Azure AD pro přístup k účtu partnerského centra. 

#### <a name="add-existing-azure-ad-applications"></a>Přidat existující aplikace Azure AD 

Přidání aplikací, která již existuje v Azure Active Directory vaší společnosti: 

1.  Z **uživatelé** stránky (v části **nastavení účtu**) vyberte **aplikacím služby Azure AD přidat**.
2.  Vyberte jednu nebo více aplikací Azure AD ze seznamu, který se zobrazí. Do vyhledávacího pole můžete použít k vyhledání specifické pro aplikace Azure AD. Pokud vyberete více než jednu aplikaci služby Azure AD přidat váš účet v partnerském centru, je nutné jim přiřadit stejné roli nebo sadu vlastních oprávnění. Chcete-li přidat více aplikací Azure AD s jinou rolí nebo oprávnění, tento postup opakujte pro každou roli nebo sadu vlastních oprávnění.
3.  Když jste hotovi s výběrem aplikace Azure AD, klikněte na tlačítko **přidat vybrané**.
5.  V **role** nastavte role nebo vlastních oprávnění pro vybranou aplikací Azure AD.
6.  Vyberte **Uložit**.

#### <a name="add-new-azure-ad-applications"></a>Přidat nové Azure AD aplikace 

Pokud chcete udělit přístup partnerského centra na zcela nové Azure účet AD aplikace, můžete si ho vytvořit v **uživatelé** oddílu. Všimněte si, že tím se vytvoří nový účet vaše společnost pracovní účet (tenanta Azure AD), nejen ve vašem účtu partnerského centra. Pokud používáte především tuto aplikaci Azure AD pro ověřování Partnerské centrum které nevyžadují uživatelé k němu přistupovat přímo, můžete zadat libovolnou platnou adresu pro **adresy URL odpovědi** a **identifikátor ID URI aplikace**, pokud Tyto hodnoty nejsou používány nástrojem jakékoli jiné aplikace Azure AD ve vašem adresáři.

1.  Z **uživatelé** stránky (v části **nastavení účtu**) vyberte **aplikacím služby Azure AD přidat**.
2.  Na další stránce vyberte **nové služby Azure AD aplikace**.
3.  Zadejte **adresy URL odpovědi** pro novou aplikaci Azure AD. Toto je adresa URL, kde uživatelé mohou přihlásit a používat aplikaci Azure AD (někdy také říká adresa URL aplikace nebo přihlašovací adresa URL). **Adresy URL odpovědi** nesmí být delší než 256 znaků a musí být jedinečný ve svém adresáři.
4.  Zadejte **identifikátor ID URI aplikace** pro novou aplikaci Azure AD. Toto je logický identifikátor pro aplikaci Azure AD, která se zobrazí při odesílání jednoho požadavku přihlašování do služby Azure AD. Všimněte si, **identifikátor ID URI aplikace** musí být jedinečný pro každou aplikaci Azure AD ve vašem adresáři. Toto ID nemůže být delší než 256 znaků. Další informace o identifikátor URI ID aplikace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#changing-the-application-registration-to-support-multi-tenant).
5.  V **role** nastavte role nebo vlastních oprávnění pro aplikaci Azure AD.
6.  Vyberte **Uložit**.

Po přidání nebo vytvořit aplikaci Azure AD, můžete se vrátit **uživatelé** a vyberte název aplikace a zkontrolujte nastavení pro aplikace, včetně ID Tenanta, ID klienta, adresa URL odpovědi a identifikátor URI ID aplikace.

#### <a name="remove-an-application"></a>Odebrání aplikace

Postup odebrání aplikace ze svého pracovního účtu (tenanta Azure AD), přejděte na **uživatelé** (v části **nastavení účtu**), vyberte aplikaci, která byste chtěli odebrat pomocí zaškrtávacího políčka ve sloupci úplně vpravo Klikněte na tlačítko **odebrat** z dostupných akcí. K potvrzení, že chcete odebrat vybrané aplikace se zobrazí automaticky otevírané okno.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Spravovat klíče pro aplikaci Azure AD

Pokud vaše aplikace Azure AD čte a zapisuje data v Microsoft Azure AD, je nutné klíč. Klíče pro aplikaci Azure AD můžete vytvořit tak, že upravíte jeho informace v partnerském centru. Můžete také odebrat klíče, které už nejsou potřeba.

1.  Z **uživatelé** stránky (v části **nastavení účtu**), vyberte název aplikace Azure AD. Zobrazí všechny aktivní klíče pro aplikaci Azure AD, včetně data, na který se vytvořil klíč a kdy vyprší platnost. 
2. Chcete-li odebrat klíč, který už je nepotřebujete, vyberte **odebrat**.
3.  Chcete-li přidat nový klíč, **přidat nový klíč**.
4.  Zobrazí se obrazovka zobrazení **ID klienta** a **hodnoty klíče**. Je potřeba k tisku nebo kopírování tyto informace nebudou moci znovu přistupovat po opuštění této stránky.
4.  Pokud chcete vytvořit další klíče, vyberte **přidat jiný klíč**.


### <a name="define-user-roles-and-permissions"></a>Definování rolí uživatelů a oprávnění

Následující role a oprávnění v komerční Marketplace programu na partnerském centru, může být přiřazena uživatelům vaší společnosti. 

Všimněte si, že se role tenanta Azure Active Directory (AAD) zahrnují globální správce, Správce uživatelů a rolí zprostředkovatel kryptografických služeb. Role non-AAD těchto rolí, které nejsou správě tenanta a který patří mezi ně správce programu MPN, obchodní profil správce, správce odkazů, pobídkové správce a pobídkové uživatele.


|**Role**|**Oprávnění**|
|----------------------------------|:---------------------------------|
|Globální správce|• Přístupné všechny Microsoft účtu nebo služby s úplnými oprávněními
|      |• Vytvářet lístky podpory pro partnerské Centrum
||• Zobrazit smlouvy, ceníky a nabídky
||• Zobrazení, vytváření a správa partnerů|
|Správce|• Ke všem funkcím účet Microsoft s výjimkou výplata a daň nastavení
|      |• Můžete spravovat uživatele, role a pracovní účty (klientů)|
|Vývojář|• Nahrát balíčky, aplikace a doplňky a zobrazit zprávu o telemetrických dat o využití
|      |• Nelze získat přístup k finanční účtu nebo informace o nastavení|
|Přispěvatel firmy|• Může získat přístup k finančním informacím a nastavit informace o cenách
|      |• Nejde vytvořit nebo odeslat nové aplikace a doplňky|
|Finanční přispěvatele|• Sestavy si můžete prohlédnout výběr
|      |• Nemůže provádět změny aplikací nebo nastavení|
|Obchodník|• Může reagovat na zapracováním připomínek zákazníků a jiné finanční sestavy
|      |• Nemůže provádět změny aplikací nebo nastavení|

Další informace o správě rolí a oprávnění v jiných oblastech partnerského centra, jako je Azure Active Directory (AD), Cloud Solution Provider (CSP), ovládací prvek Panel dodavatele (CPV), uživatelé typu Host nebo Microsoft Partner Network (MPN), přečtěte si téma [přiřazení role uživatelů a oprávnění v partnerském centru](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Správa Tenantů

Tenanta služby Azure Active Directory (AD), označují také jako "pracovní účet" v této dokumentaci je reprezentace vaší organizaci nastavit na webu Azure Portal a pomáhá spravovat konkrétní instanci cloudovými službami Microsoftu pro váš interní a externí uživatele. Pokud vaše organizace předplatné služby do cloudové služby Microsoftu, jako je například Azure, Microsoft Intune nebo Office 365 tenanta služby Azure AD bylo vytvořeno za vás. 

Můžete nastavit více tenantů pro použití s partnerského centra. Každý uživatel s **správce** role v partnerském centru účtu budou mít možnost přidávat a odebírat tenantů Azure AD z účtu.  

### <a name="add-an-existing-tenant"></a>Přidání existujícího tenanta

Chcete přidružit účet v partnerském centru jiným tenantem Azure AD:

1.  Z **Tenantů** stránky (v části **nastavení účtu**) vyberte **přidružit jiným tenantem Azure AD**.
2. Zadejte svoje přihlašovací údaje Azure AD pro tenanta, který chcete přidružit.
3. Zkontrolujte název organizace a doménu pro tenanta služby Azure AD. K dokončení přidružení, zvolit **potvrdit**.

Pokud přidružení je úspěšné, pak budete připraveni přidat a spravovat uživatele účtu v **uživatelé** části v partnerském centru.

### <a name="create-a-new-tenant"></a>Vytvořit nového tenanta

Vytvoření značky nové služby Azure AD tenanta se váš účet v partnerském centru:

1.  Z **Tenantů** stránky (v části **nastavení účtu**) vyberte **vytvořit nový tenant Azure AD**.
2. Zadejte informace o adresáře pro vaši novou službu Azure AD:
    - **Název domény**: Jedinečný název, který použijeme pro doménu vaší služby Azure AD, spolu s ". onmicrosoft.com". Pokud jste zadali "vzorový", může být svoji doménu Azure AD jako například "example.onmicrosoft.com".
    - **Kontaktní e-mail**: E-mailovou adresu, kde budeme vás moc kontaktovat ohledně vašeho účtu v případě potřeby.
    - **Informace o uživatelském účtu globálního správce**: Křestní jméno, poslední název, uživatelské jméno a heslo, které chcete použít pro nový účet globálního správce.
3. Vyberte **vytvořit** potvrďte nové informace o doméně a účtu.
4. Přihlaste se pomocí nového Azure AD globálního správce uživatelského jména a hesla zahájíte [přidávání a Správa uživatelů](#manage-users).

Další informace o vytváření nových tenantů v rámci webu Azure portal, nikoli přes portál partnerském centru, najdete v článku [vytvořit nového tenanta v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Odeberte klienta

Odebrání klienta z váš účet v partnerském centru, najdete na jeho název **Tenantů** stránky (v **nastavení účtu**) a pak vyberte **odebrat**. Budete vyzváni k potvrzení odebrání klienta. Až to uděláte tak, žádné uživatelé v tomto tenantovi se nebudou moct přihlásit k účtu partnerského centra a odeberou se všechna oprávnění, které jste nakonfigurovali pro tyto uživatele.

Když odeberete tenanta, všichni uživatelé, které jste přidali účet v partnerském centru z tohoto tenanta už bude moct přihlásit k účtu.

> [!TIP]
> Pokud jste aktuálně přihlášeni do partnerského centra ve stejném tenantovi pomocí účtu, nemůžete odebrat tenanta. Chcete-li odebrat klienta, musíte se přihlásit do partnerského centra jako **správce** u jiného tenanta, která je přidružená k účtu. Pokud pouze jednoho tenanta přidruženého k účtu tohoto tenanta lze odebrat pouze po přihlášení pomocí účtu Microsoft, které se otevřelo účet.


## <a name="agreements"></a>Smlouvy

**Smlouvy** část partnerského centra (v části **nastavení účtu**) umožňuje zobrazit seznam publikování smlouvy, které jste autorizovali. Tyto smlouvy jsou uvedeny podle názvu a čísla verze, včetně data, kdy byla přijata a jméno uživatele, který přijmout smlouvu. 

**Akce potřebné** může zobrazit v horní části této stránky, pokud jsou k dispozici aktualizace smlouvy, které vyžadují vaši pozornost. Přijmout aktualizované smlouvě, nejprve přečíst propojená verze smlouvy a pak vyberte **přijměte podmínky smlouvy**. 

Informace o Cloud Solution Provider (CSP) smluv v partnerském centru [smlouvy Microsoft cloudu podle oblasti a jazyka](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Další postup

- [Vytvoření nové nabídky SaaS](./create-new-saas-offer.md)
