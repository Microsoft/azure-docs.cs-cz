---
title: Kurz konfigurace N8 identity pomocí Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Kurz pro konfiguraci nástroje pro správu TheAccessHub s Azure Active Directory B2C pro řešení migrace zákaznických účtů a správy žádostí o služby zákazníkům (CSR)
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a6d6ca825a556ea3c98fb94d4becbb75b8f2a7d7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294276"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci nástroje pro správu TheAccessHub pomocí Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny, jak integrovat Azure Active Directory (AD) B2C s [nástrojem Správce TheAccessHub](https://n8id.com/products/theaccesshub-admintool/) z identity N8. Toto řešení řeší migraci účtů zákazníků a správu žádostí o služby zákazníkům (CSR).  

Toto řešení je vhodné pro vás, pokud máte jeden nebo více následujících požadavků:

- Máte existující web a chcete provést migraci na Azure AD B2C. Působit potíže se ale migrace vašich účtů zákazníků, včetně hesel.

- Vyžadujete, aby váš CSR nástroj mohl spravovat účty Azure AD B2C.

- Máte požadavek na použití delegované správy pro vaše zástupce.

- Chcete synchronizovat a sloučit data z mnoha úložišť do Azure AD B2C.

## <a name="pre-requisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Tenant musí být propojený s vaším předplatným Azure.

- Prostředí pro správu TheAccessHub: kontaktuje [identitu N8](https://n8id.com/contact/) a zřídí nové prostředí.

- Volitelné Informace o připojení a přihlašovacích údajích pro všechny databáze nebo protokoly LDAP (Lightweight Directory Access Protocol), ze kterých chcete migrovat zákaznická data.

- Volitelné Nakonfigurované Azure AD B2C prostředí pro použití [vlastních zásad](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started), pokud chcete integrovat nástroj TheAccessHub admin do svého toku zásad registrace.

## <a name="scenario-description"></a>Popis scénáře

Nástroj pro správu TheAccessHub se spouští jako jakákoli jiná aplikace v Azure. Může běžet buď s předplatným Azure N8 identity, nebo s předplatným zákazníka. V následujícím diagramu architektury se zobrazuje implementace.

![Obrázek znázorňující diagram architektury n8identity](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel dorazí na přihlašovací stránku. Uživatelé si vyberou přihlášení a vytvoří nový účet a na stránce se zadají informace. Azure AD B2C shromažďuje atributy uživatele.
| 2. | Azure AD B2C volá Nástroj pro správu TheAccessHub a předává atributy uživatele.
| 3. | Nástroj pro správu TheAccessHub zkontroluje stávající databázi pro aktuální informace o uživateli.  
| 4. | Záznam uživatele se synchronizuje z databáze do nástroje pro správu TheAccessHub.
| 5. | Nástroj pro správu TheAccessHub sdílí data s delegovaným správcem nebo helpdeskem.
| 6. | Nástroj pro správu TheAccessHub synchronizuje záznamy uživatelů s Azure AD B2C.
| 7. |Na základě odpovědi na úspěch nebo neúspěch z nástroje pro správu TheAccessHub Azure AD B2C odešle uživateli přizpůsobený uvítací e-mail.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Vytvoření globálního správce ve vašem tenantovi Azure AD B2C

Nástroj pro správu TheAccessHub vyžaduje oprávnění jednat jménem globálního správce ke čtení informací o uživateli a provádění změn ve vašem tenantovi Azure AD B2C. Změny vašich řádných správců; TheAccessHub schopnost nástroje pro správu s klientem pracovat.

Pokud chcete vytvořit globálního správce, postupujte následovně:

1. V Azure Portal se přihlaste k tenantovi Azure AD B2C jako správce. Přejít na **Azure Active Directory**  >  **uživatelů**
2. Vybrat **nového uživatele**
3. Zvolením možnosti **vytvořit uživatele** můžete vytvořit pravidelného uživatele adresáře, nikoli zákazníka.
4. Vyplňte formulář informace o identitě.

   a. Zadejte uživatelské jméno, například ' theaccesshub '.

   b. Zadejte název, jako je například ' TheAccessHub Service Account '.

5. Vyberte možnost **Zobrazit heslo** a zkopírujte počáteční heslo pro pozdější použití.
6. Přiřazení role globálního správce

   a. Vyberte **uživatele** aktuálních rolí uživatele, které chcete změnit.

   b. Ověřte záznam pro globálního správce.

   c. **Vybrat**  >  **Vytvořit**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Připojení nástroje pro správu TheAccessHub k vašemu tenantovi Azure AD B2C

Nástroj TheAccessHub admin Tool používá Graph API Microsoftu ke čtení a provádění změn v adresáři. Funguje jako globální správce vašeho tenanta. Správce TheAccessHub vyžaduje další oprávnění, které je možné udělit v rámci tohoto nástroje.

Pokud chcete pro přístup k adresáři autorizovat Nástroj pro správu TheAccessHub, postupujte takto:

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Přejděte do **správce systému**  >  **Azure AD B2C config** .

3. Vyberte **autorizovat připojení** .

4. V novém okně se přihlaste pomocí účtu globálního správce. Pokud se k novému účtu služby přihlašujete poprvé, může se vám zobrazit výzva k resetování hesla.

5. Postupujte podle zobrazených výzev a vyberte **přijmout** a udělte tak nástroji Správce TheAccessHub požadovaná oprávnění.

## <a name="configure-a-new-csrhelpdesk-user-using-your-enterprise-identity"></a>Konfigurace nového uživatele CSR/helpdesku pomocí podnikové identity

Vytvořte uživatele CSR/helpdesku, který přistupuje k nástroji pro správu TheAccessHub pomocí svých stávajících přihlašovacích údajů pro podnikové Azure Active Directory.

Pokud chcete nakonfigurovat uživatele CSR/helpdesku pomocí jednotného přihlašování (SSO), doporučují se tyto kroky:

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů poskytnutých identitou N8.

2. Navigace do **nástrojů Správce**  >  **Správa kolegů**

3. Vyberte **Přidat kolegu** .

4. Vyberte **kolegový typ správce Azure**

5. Zadejte informace o profilu kolegu.

   a. Výběr domácí organizace bude řídit, kdo má oprávnění ke správě tohoto uživatele.

   b. Pro ID přihlášení/uživatelské jméno Azure AD zadejte hlavní název uživatele z účtu Azure Active Directory uživatele.

   c. Na kartě role TheAccessHub se podívejte na helpdesk spravované role. Umožní uživateli přístup k zobrazení Správa kolegů. Uživatel bude muset být stále umístěný do skupiny nebo musí být vlastníkem organizace, aby na zákazníky pracoval.

6. Vyberte **Odeslat**.

## <a name="configure-a-new-csrhelpdesk-user-using-a-new-identity"></a>Konfigurace nového uživatele CSR/helpdesku pomocí nové identity

Vytvořte uživatele CSR/helpdesku, který bude mít přístup k nástroji pro správu TheAccessHub s novým místním přihlašovacím údajem, který je jedinečný pro nástroj TheAccessHub admin. Budou se používat hlavně organizacím, které pro svůj podnik nepoužívají službu Azure AD.

Pokud chcete [nastavit uživatele CSR/helpdesku](https://youtu.be/iOpOI2OpnLI) bez jednotného přihlašování, postupujte takto:

1. Přihlášení k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů poskytnutých identitou N8

2. Navigace do **nástrojů Správce**  >  **Správa kolegů**

3. Vyberte **Přidat kolegu** .

4. Vyberte **kolegový typ místní správce** .

5. Zadejte informace o profilu kolegu.

   a. Výběr domácí organizace bude řídit, kdo má oprávnění ke správě tohoto uživatele.

   b. Na kartě **role TheAccessHub** vyberte **helpdesku** spravované role. Umožní uživateli přístup k zobrazení Správa kolegů. Uživatel bude muset být stále umístěný do skupiny nebo musí být vlastníkem organizace, aby na zákazníky pracoval.

6. Zkopírujte atributy **přihlašovací ID/e-mail** a **jednorázové heslo** . Poskytněte novému uživateli. Použijí tyto přihlašovací údaje k přihlášení k nástroji pro správu TheAccessHub. Uživateli se zobrazí výzva k zadání nového hesla při prvním přihlášení.

7. Vybrat **Odeslat**

## <a name="configure-partitioned-csrhelpdesk-administration"></a>Konfigurace oddělení IT/správy helpdesku

Oprávnění ke správě uživatelů a oddělení služeb zákazníkům a helpdesku v nástroji pro správu TheAccessHub se spravují pomocí hierarchie organizace. Všichni kolegové a zákazníci mají domovskou organizaci, kde se nacházejí. Konkrétní kolegy nebo skupiny kolegů se dají přiřadit jako vlastníci organizací.  Vlastníci organizace můžou spravovat (dělat změny) kolegů a zákazníků v organizacích nebo podorganizacích, které vlastní. Pokud chcete více kolegům dovolit spravovat skupinu uživatelů, je možné vytvořit skupinu s mnoha členy. Skupinu je pak možné přiřadit jako vlastník organizace a všichni členové skupiny můžou spravovat kolegy a zákazníky v organizaci.

### <a name="create-a-new-group"></a>Vytvoření nové skupiny

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí **přihlašovacích údajů** , které jste poskytli N8 identity.

2. Přejděte na **organizaci > Správa skupin**

3. Vyberte > **Přidat skupinu** .

4. Zadejte **název skupiny** , **Popis skupiny** a **vlastníka skupiny** .

5. Vyhledejte a zaškrtněte políčka u kolegů, které chcete být členy skupiny, a pak vyberte > **Přidat**

6. V dolní části stránky vidíte všechny členy skupiny.

7. Pokud je nutné, aby se členové mohli odebrat, vyberte **x** na konci řádku.

8. Vybrat **Odeslat**

### <a name="create-a-new-organization"></a>Vytvoření nové organizace

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Navigace v organizaci > **Správa organizací**

3. Vyberte > **Přidat organizaci** .

4. Zadejte **název organizace** , **vlastníka organizace** a **nadřazenou organizaci**.

    a. Název organizace je v ideálním případě hodnota, která odpovídá zákaznickým datům. Pokud při načítání zákaznických a zákaznických dat zadáte název organizace do zatížení, může být kolega automaticky umístěný do organizace.

    b. Vlastník představuje osobu nebo skupinu, kteří budou spravovat zákazníky a kolegy v této organizaci a v jakékoli suborganizaci v rámci.

    c. Nadřazená organizace indikuje, která z nich je v podstatě i v rámci této organizace.

5. Vyberte **Odeslat**.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Úprava hierarchie prostřednictvím stromového zobrazení

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Přejít na **Manager Tools**  >  **stromové zobrazení** nástrojů pro správu

3. V této prezentaci můžete vizualizovat, kteří kolegové a skupiny mohou spravovat, které organizace.

4. Hierarchie lze upravit přetažením organizací overtop organizacím, které mají být nadřazené.

5. Po dokončení změny hierarchie vyberte **Uložit** .

## <a name="customize-welcome-notification"></a>Přizpůsobit uvítací oznámení

Když používáte TheAccessHub k migraci uživatelů z předchozího řešení ověřování do Azure AD B2C, možná budete chtít přizpůsobit uvítací oznámení uživatele, které se uživateli pošle TheAccessHub během migrace. Tato zpráva obvykle obsahuje odkaz na zákazníka, který nastaví nové heslo v adresáři Azure AD B2C.

Postup při přizpůsobování oznámení:

1. Přihlaste se k TheAccessHub pomocí přihlašovacích údajů, které vám poskytla N8 identita.

2. Přejít na **oznámení správce systému**  >  **Notifications**

3. Vyberte **šablonu vytvořit kolegu** .

4. Vybrat **Upravit**

5. V případě potřeby upravte pole zprávy a šablony. Pole Šablona má na paměti HTML a může odesílat oznámení ve formátu HTML na e-maily zákazníků.

6. Po dokončení vyberte **Uložit** .

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Migrace dat z externích zdrojů dat do Azure AD B2C

Pomocí nástroje pro správu TheAccessHub můžete importovat data z různých databází, LDAPs a souborů CSV a potom tato data nasdílet do svého tenanta Azure AD B2C. To se provádí načtením dat do typu kolegu Azure AD B2C uživatele v rámci nástroje pro správu TheAccessHub.  Pokud zdrojem dat není samotný Azure, data se umístí do obou TheAccessHub nástrojů pro správu a Azure AD B2C. Pokud zdroj externích dat není na vašem počítači jednoduchý soubor. csv, před provedením načtení dat nastavte zdroj dat. Níže uvedené kroky popisují vytvoření zdroje dat a načtení dat.

### <a name="configure-a-new-data-source"></a>Konfigurace nového zdroje dat

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Přejít do **System Admin**  >  **zdrojů dat** správce systému

3. Vyberte **Přidat zdroj dat** .

4. Zadat **název** a **typ** pro tento zdroj dat

5. Vyplňte data formuláře v závislosti na typu zdroje dat:

   **Pro databáze**

   a. **Typ** – databáze

   b. **Typ databáze** – vyberte databázi z některého z podporovaných typů databáze.

   c. **Adresa URL připojení** – zadejte dobře formátovaný připojovací řetězec JDBC. Například: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Uživatelské jméno** – zadejte uživatelské jméno pro přístup k databázi.

   e. **Heslo** – zadejte heslo pro přístup do databáze.

   f. **Dotaz** – zadejte dotaz SQL pro extrakci podrobností o zákaznících. Například: ``SELECT * FROM mytable;``

   například Vyberte **Test připojení**. zobrazí se ukázka vašich dat, která zajistí, že připojení funguje.

   **Pro LDAPs**

   a. **Typ** – LDAP

   b. **Hostitel** – zadejte název hostitele nebo IP adresu počítače, na kterém běží server LDAP. Například: ``mysite.com``

   c. **Port** – zadejte číslo portu, ve kterém server LDAP naslouchá.

   d. **SSL** – zaškrtněte políčko, pokud má nástroj pro správu TheAccessHub komunikovat s protokolem LDAP zabezpečeně pomocí protokolu SSL. Důrazně doporučujeme používat protokol SSL.

   e. **Přihlašovací jméno přihlášení** – zadejte rozlišující název uživatelského účtu, ke kterému se chcete přihlásit, a proveďte hledání LDAP.

   f. **Heslo** – zadejte heslo pro uživatele.

   například **Základní rozlišující název** – zadejte rozlišující název v horní části hierarchie, ve které chcete hledat.

   h. **Filtr** – zadejte řetězec filtru protokolu LDAP, který získá záznamy o zákaznících.

   i. **Atributy** – Zadejte čárkami oddělený seznam atributů ze záznamů zákazníků, které se mají předat nástroji pro správu TheAccessHub.

   j. Vyberte **Test připojení**. zobrazí se ukázka vašich dat, která zajistí, že připojení funguje.

   **Pro OneDrive**

   a. **Typ** – OneDrive pro firmy

   b. Vyberte **autorizovat připojení** .

   c. Nové okno vás vyzve, abyste se přihlásili k **OneDrivu**. Přihlaste se pomocí uživatele s přístupem pro čtení k vašemu účtu OneDrive. Nástroj pro správu TheAccessHub bude tomuto uživateli působit, aby četl soubory ve formátu CSV.

   d. Postupujte podle zobrazených výzev a vyberte **přijmout** a udělte tak nástroji Správce TheAccessHub požadovaná oprávnění.

6. Po dokončení vyberte **Uložit** .  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Synchronizovat data ze zdroje dat do Azure AD B2C

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Přejít na **System Admin**  >  **synchronizaci dat** správce systému

3. Vybrat **nové zatížení**

4. Vyberte **typ kolegu** Azure AD B2C uživatele.

5. V automaticky otevíraném dialogovém okně vyberte **zdroj** dat. Pokud jste vytvořili zdroj dat OneDrive, vyberte také soubor.

6. Pokud s tímto zatížením nechcete vytvářet nové účty zákazníků, změňte první zásadu: **Pokud kolega nebyl nalezen v TheAccessHub** , **neprovádějte žádnou akci** .

7. Pokud s tímto zatížením nechcete aktualizovat existující účty zákazníků, změňte druhou zásadu, pokud se **neshodují zdroje a TheAccessHubá data** , a pak **neprovádějte žádnou** akci.

8. Vyberte **Další**.

9. V **konfiguraci mapování vyhledávání** identifikujeme, jak sladit záznamy zatížení se zákazníky, kteří už jsou načetli do nástroje pro správu TheAccessHub. Vyberte jeden nebo více identifikačních atributů ve zdroji. Porovnává s atributy s atributem v nástroji Správce TheAccessHub, který obsahuje stejné hodnoty. Pokud se najde shoda, existující záznam se přepíše. v opačném případě se vytvoří nový zákazník. Můžete sekvencovat několik těchto kontrol. Můžete například nejprve kontrolovat e-mail a pak jméno a příjmení.

10. V nabídce na levé straně vyberte **mapování dat**.

11. V konfiguraci Data-Mapping přiřaďte, které atributy nástroje pro správu TheAccessHub byste měli naplnit ze svých zdrojových atributů. Není nutné namapovat všechny atributy. Nemapované atributy zůstanou u stávajících zákazníků beze změny.

12. Pokud namapujete na atribut org_name s hodnotou, která je názvem existující organizace, budou noví zákazníci v této organizaci umístění.

13. Vyberte **Další**.

14. V případě, že by mělo být toto zatížení znovu nabíhat, může být zadáno denní/týdenní nebo měsíční plán. V opačném případě **ponechte výchozí hodnotu**.

15. Vybrat **Odeslat**

16. Pokud jste zvolili **plán nyní** , nový záznam se okamžitě přidá na obrazovku synchronizace dat. Jakmile fáze ověření dosáhne 100%, vyberte **nový záznam** , abyste viděli očekávaný výsledek načtení. Pro naplánovaná zatížení se tyto záznamy zobrazí až po naplánovaném čase.

17. Pokud nedošlo k chybám, vyberte **Spustit** a potvrďte změny. V opačném případě v nabídce **Další** vyberte **Odebrat** a odstraňte zatížení. Pak můžete opravit zdrojová data nebo načíst mapování a zkusit to znovu. Pokud je počet chyb malý, můžete záznamy aktualizovat ručně a vybrat možnost **aktualizovat** u každého záznamu a provést opravy. Nakonec můžete pokračovat v jakýchkoli chybách a později je vyřešit jako **zákroky podpory** v nástroji pro správu TheAccessHub.

18. Pokud bude záznam **synchronizace dat** ve fázi zatížení 100%, budou inicializovány všechny změny, které byly výsledkem zatížení. Zákazníci by měli začít zobrazovat nebo přijímat změny v Azure AD B2C.

## <a name="synchronize-azure-ad-b2c-customer-data-into-theaccesshub-admin-tool"></a>Synchronizace zákaznických dat Azure AD B2C do nástroje pro správu TheAccessHub

Nástroj pro správu TheAccessHub může v rámci jednorázové nebo průběžné operace synchronizovat všechny informace o zákaznících z Azure AD B2C do nástroje pro správu TheAccessHub. Tím se zajistí, že se správcům oddělení technické podpory a helpdesku zobrazují aktuální informace o zákaznících.

Synchronizace dat z Azure AD B2C do nástroje pro správu TheAccessHub:

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Přejít na **System Admin**  >  **synchronizaci dat** správce systému

3. Vybrat **nové zatížení**

4. Vyberte **typ kolegu** Azure AD B2C uživatele.

5. Pro krok **Možnosti** ponechte výchozí nastavení.

6. Vyberte **Další**.

7. Pro **mapování dat & krok hledání** ponechte výchozí nastavení. S výjimkou toho, že Pokud namapujete na atribut **org_name** s hodnotou, která je názvem existující organizace, budou noví zákazníci vytvořeni v této organizaci.

8. Vyberte **Další**.

9. V případě, že by mělo být toto zatížení znovu nabíhat, může být zadáno denní/týdenní nebo měsíční plán. V opačném případě ponechte výchozí nastavení: **nyní**. Doporučujeme pravidelně synchronizovat z Azure AD B2C.

10. Vybrat **Odeslat**

11. Pokud jste zvolili plán **nyní** , nový záznam se okamžitě přidá na obrazovku synchronizace dat. Jakmile fáze ověření dosáhne 100%, vyberte nový záznam, abyste viděli očekávaný výsledek načtení. Pro naplánovaná zatížení se tyto záznamy zobrazí až po naplánovaném čase.

12. Pokud nedošlo k chybám, vyberte **Spustit** a potvrďte změny. V opačném případě v nabídce Další vyberte **Odebrat** a odstraňte zatížení. Pak můžete opravit zdrojová data nebo načíst mapování a zkusit to znovu. Pokud je počet chyb malý, můžete záznamy aktualizovat ručně a vybrat možnost **aktualizovat** u každého záznamu a provést opravy. Nakonec můžete pokračovat v jakýchkoli chybách a později je vyřešit jako zákroky podpory v nástroji pro správu TheAccessHub.

13. Pokud bude záznam **synchronizace dat** ve fázi zatížení 100%, budou inicializovány všechny změny, které byly výsledkem zatížení.

## <a name="configure-azure-ad-b2c-policies-to-call-theaccesshub-admin-tool"></a>Konfigurace zásad Azure AD B2C pro volání nástroje pro správu TheAccessHub

Občas synchronizování nástroje pro správu TheAccessHub je omezené jeho schopnost udržovat svůj stav aktuální Azure AD B2C. K informování TheAccessHub nástrojů pro správu TheAccessHub můžete využít zásady rozhraní API a Azure AD B2C nástroje pro správu. Toto řešení vyžaduje technické znalosti [Azure AD B2C vlastní zásady](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started#:~:text=%20Get%20started%20with%20custom%20policies%20in%20Azure,Experience%20Framework%20applications.%20Azure%20AD%20B2C...%20More%20). V další části vám poskytneme ukázkový postup a zabezpečený certifikát pro oznamování TheAccessHub nástroje pro správu nových účtů v Sign-Up vlastních zásadách.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Vytvoření zabezpečeného pověření pro vyvolání rozhraní API nástroje pro správu TheAccessHub

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Přejít do nástroje pro správu **správce systému**  >  **Admin Tools**  >  **zabezpečení rozhraní API**

3. Vyberte **Generovat**

4. Zkopírujte **heslo certifikátu** .

5. Vyberte **Stáhnout** a získejte klientský certifikát.

6. Podle tohoto [kurzu](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#https-client-certificate-authentication ) přidejte certifikát klienta do Azure AD B2C.

### <a name="retrieve-your-custom-policy-examples"></a>Příklady získání vlastních zásad

1. Přihlaste se k nástroji pro správu TheAccessHub pomocí přihlašovacích údajů, které jste poskytli N8 identity.

2. Navigace do nástroje pro správu **správce systému**  >  **Admin Tools**  >  **zásady Azure B2C**

3. Zadejte svou doménu tenanta Azure AD B2C a dvě ID rozhraní identity Experience Framework z konfigurace architektury identity Experience Framework.

4. Vyberte **Uložit**.

5. Vyberte **Stáhnout** a získejte soubor zip se základními zásadami, které přidávají zákazníky do nástroje pro správu TheAccessHub jako registrace zákazníků.

6. V tomto [kurzu](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) začnete s návrhem vlastních zásad v Azure AD B2C.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)