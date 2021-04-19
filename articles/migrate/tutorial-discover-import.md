---
title: Vyhodnocení místních serverů pomocí importovaného souboru CSV s Azure Migrate posouzení serveru
description: Popisuje, jak zjišťovat místní servery pro migraci do Azure pomocí importovaného souboru CSV v Azure Migrate posouzení serveru.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: c142cae3e96d800488b67da613181d1a91ba5b5b
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713313"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Kurz: posouzení serverů pomocí importovaného souboru CSV

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení. 

V tomto kurzu se dozvíte, jak vyhodnotit místní počítače pomocí nástroje Azure Migrate: Server Assessment Tool pomocí importovaného souboru s oddělovači (CSV). 

Pokud používáte soubor CSV, nemusíte nastavovat zařízení Azure Migrate, abyste mohli zjišťovat a vyhodnocovat servery. Data, která v souboru sdílíte, můžete řídit a většina dat je volitelná. Tato metoda je užitečná v případě, že:

- Chcete vytvořit rychlé počáteční posouzení před nasazením zařízení.
- Nemůžete ve vaší organizaci nasadit zařízení Azure Migrate.
- Nemůžete sdílet přihlašovací údaje umožňující přístup k místním serverům.
- Kvůli omezením zabezpečení nemůžete shromažďovat a odesílat data shromážděná zařízením do Azure.

> [!NOTE]
> Nemůžete migrovat servery importované pomocí souboru CSV.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení účtu Azure
> * Nastavení projektu Azure Migrate
> * Příprava souboru CSV
> * Import souboru
> * Vyhodnotit servery

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Požadavky

- Do jednoho souboru CSV a do Azure Migrateho projektu můžete přidat až 20 000 serverů. 
- Názvy operačních systémů zadané v souboru CSV musí obsahovat a odpovídat [podporovaným názvům](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate, budete potřebovat účet s:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory.

Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby** vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-import/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup** vyhledejte příslušný uživatelský účet.
5. V **Přidat přiřazení role** vyberte **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-import/azure-account-access.png)

6. V části **Přidat přiřazení role** vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Pak vyberte **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-import/assign-role.png)

7. Na portálu vyhledejte uživatele a v části **služby** vyberte **Uživatelé**.
8. V **nastavení uživatele** ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt Azure Migrate, pokud ho ještě nemáte.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu** vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt** vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-import/new-project.png)  
    > [!Note]
    > Pomocí oddílu **Pokročilé** konfigurace můžete vytvořit Azure Migrate projekt s připojením privátního koncového bodu. [Další informace](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí.

**Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Příprava souboru CSV

Stáhněte si šablonu CSV a přidejte do ní informace o serverech.

### <a name="download-the-template"></a>Stažení šablony

1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Hodnocení serverů** vyberte **Zjistit**.
2. Na stránce **Zjistit počítače** vyberte **Importovat pomocí souboru CSV**.
3. Vyberte **Stáhnout** a stáhněte si šablonu CSV. Případně si ji můžete [stáhnout přímo](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Stažení šablony CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Přidání informací o serverech

Shromážděte data serverů a přidejte je do souboru CSV.

- Data můžete shromáždit tak, že je exportujete z nástrojů, které používáte ke správě místních serverů, jako jsou VMware vSphere nebo vaše databáze CMDB (Configuration Management Database).
- Pokud si chcete projít ukázková data, stáhněte si náš [ukázkový soubor](https://go.microsoft.com/fwlink/?linkid=2108405).

Následující tabulka shrnuje pole v souboru, která se mají vyplnit:

**Název pole** | **Povinné** | **Podrobnosti**
--- | --- | ---
**Název serveru** | Yes | Doporučujeme zadat plně kvalifikovaný název domény (FQDN).
**IP adresa** | No | Adresa serveru
**Cores** | Yes | Počet jader procesoru přidělených serveru
**Memory (Paměť)** | Yes | Celková velikost paměti RAM v MB přidělené serveru
**Název operačního systému** | Yes | Operační systém serveru. <br/> Posouzení rozpoznává názvy operačních systémů uvedené v [tomto](#supported-operating-system-names) seznamu (nebo názvy, které je obsahují).
**Verze operačního systému** | No | Verze operačního systému serveru
**Architektura operačního systému** | No | Architektura operačního systému serveru. <br/> Platné hodnoty: x64, x86, amd64, 32bitová nebo 64bitová
**Počet disků** | No | Není potřeba, pokud zadáte podrobnosti o jednotlivých discích.
**Velikost disku 1**  | No | Maximální velikost disku v GB.<br/>Podrobnosti o dalších discích můžete přidat tak, že do šablony [přidáte sloupce](#add-multiple-disks). Můžete přidat až osm disků.
**Operace čtení z disku 1** | No | Operace čtení z disku za sekundu
**Operace zápisu na disk 1** | No | Operace zápisu na disk za sekundu
**Propustnost čtení z disku 1** | No | Data načtená z disku za sekundu v MB/s
**Propustnost zápisu na disk 1** | No | Data zapsaná na disk za sekundu v MB/s
**Procento využití procesoru** | No | Procento využitého procesoru
**Procento využití paměti** | No | Procento využité paměti RAM
**Celkový počet operací čtení z disků** | No | Operace čtení disku za sekundu
**Celkový počet operací zápisu na disky** | No | Operace zápisu na disk za sekundu
**Celková propustnost čtení z disků** | No | Data načtená z disku v MB za sekundu.
**Celková propustnost zápisu na disky** | No | Data zapsaná na disk v MB za sekundu.
**Propustnost síťových vstupů** | No | Data přijatá serverem v MB/s
**Propustnost síťových výstupů** | No | Data odeslaná serverem v MB/s
**Typ firmwaru** | No | Firmware serveru. Možné hodnoty: BIOS nebo UEFI.
**Adresa MAC**| No | Adresa MAC serveru


### <a name="add-operating-systems"></a>Přidání operačních systémů

Posouzení rozpoznává konkrétní názvy operačních systémů. Všechny názvy, které zadáte, se musí přesně shodovat s některým z řetězců v [seznamu podporovaných názvů](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Přidání více disků

Šablona obsahuje výchozí pole pro první disk. Můžete přidat podobné sloupce až pro osm disků.

Například pokud chcete zadat všechna pole pro druhý disk, přidejte tyto sloupce:

- Velikost disku 2
- Operace čtení z disku 2
- Operace zápisu na disk 2
- Propustnost čtení z disku 2
- Propustnost zápisu na disk 2


## <a name="import-the-server-information"></a>Import informací o serverech

Po přidání informací do šablony sdíleného svazku clusteru importujte soubor CSV do vyhodnocování serveru.

1. Ve službě Azure Migrate na stránce **Zjistit počítače** přejděte k dokončené šabloně.
2. Vyberte **Importovat**.
3. Zobrazí se stav importu.
    - Pokud se ve stavu zobrazí upozornění, můžete je vyřešit nebo pokračovat bez jejich vyřešení.
    - Pokud chcete zvýšit přesnost posouzení, zlepšete informace o serverech podle pokynů v upozorněních.
    - Pokud chcete zobrazit a vyřešit upozornění, vyberte **Stáhnout soubor CSV s podrobnostmi o upozorněních**. Tato operace stáhne soubor CSV s upozorněními. Zkontrolujte upozornění a podle potřeby vyřešte problémy.
    - Pokud se ve stavu zobrazí chyby a stav importu je **Selhání**, musíte tyto chyby opravit, abyste mohli pokračovat v importu:
        1. Stáhněte si soubor CSV, který teď obsahuje podrobnosti o chybách.
        1. Zkontrolujte tyto chyby a podle potřeby je opravte. 
        1. Znovu nahrajte upravený soubor.
4. Když je stav importu **Dokončeno**, znamená to, že se informace o serverech importovaly. Aktualizujte, pokud se zdá, že proces importu není dokončen.

## <a name="update-server-information"></a>Aktualizace informací o serverech

Informace o serveru můžete aktualizovat opětovným importem dat o serveru s použitím stejného **názvu serveru**. Pole **Název serveru** nemůžete upravit. Odstraňování serverů se v současné době nepodporuje.

## <a name="verify-servers-in-the-portal"></a>Ověření serverů na portálu

Ověření zobrazování serverů na webu Azure Portal po zjišťování:

1. Otevřete řídicí panel služby Azure Migrate.
2. Na stránce **Azure Migrate – Servery** > **Azure Migrate: Hodnocení serverů** vyberte ikonu s počtem **zjištěných serverů**.
3. Vyberte kartu **Na základě importu**.



## <a name="supported-operating-system-names"></a>Podporované názvy operačních systémů

Názvy operačních systémů, které jsou k dispozici ve sdíleném svazku clusteru, musí obsahovat a odpovídat. Pokud ne, nebudete je moci vyhodnotit. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>macOS × 10<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity systémy eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření projektu Azure Migrate 
> * Zjištěné servery s použitím importovaného souboru CSV. Nyní spusťte posouzení [migrace virtuálních počítačů VMware do virtuálních počítačů Azure](./tutorial-assess-vmware-azure-vm.md).