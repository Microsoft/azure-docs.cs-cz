---
title: Zjišťování místních serverů pomocí importovaného souboru CSV s Azure Migrate posouzení serveru
description: Popisuje, jak zjišťovat místní servery pro migraci do Azure pomocí importovaného souboru CSV v Azure Migrate posouzení serveru.
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 6526961df225e4f347216428141e8217043161df
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064254"
---
# <a name="tutorial-discover-servers-using-an-imported-csv-file"></a>Kurz: zjišťování serverů pomocí importovaného souboru CSV

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení. 

V tomto kurzu se dozvíte, jak pomocí importovaného souboru s oddělovači (CSV) vyhledat místní virtuální počítače VMware pomocí nástroje Azure Migrate: Server Assessment Tool. 

Pokud používáte soubor CSV, nemusíte pro zjišťování serverů nastavovat zařízení Azure Migrate. Data, která v souboru sdílíte, můžete řídit a většina dat je volitelná. Tato metoda je užitečná v případě, že:

- Před nasazením zařízení chcete vytvořit rychlé a počáteční posouzení.
- Zařízení Azure Migrate ve vaší organizaci nemůžete nasadit.
- Nemůžete sdílet přihlašovací údaje, které povolují přístup k místním serverům.
- Omezení zabezpečení brání v shromažďování a odesílání dat shromážděných zařízením do Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení účtu Azure
> * Nastavte Azure Migrate projekt.
> * Příprava souboru CSV
> * Importujte soubor.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné. 

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Předpoklady

- Do jednoho souboru CSV a do Azure Migrateho projektu můžete přidat až 20 000 serverů. 
- Názvy operačních systémů zadané v souboru CSV musí obsahovat a odpovídat [podporovaným názvům](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s tímto:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory.

Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby**vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-import/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup**vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role**klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-import/azure-account-access.png)

6. V části **Přidat přiřazení role**vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-import/assign-role.png)

7. Na portálu vyhledejte uživatele a v části **služby**vyberte **Uživatelé**.
8. V **nastavení uživatele**ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu**vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt**vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-import/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí.

**Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Příprava sdíleného svazku clusteru

Stáhněte si šablonu sdíleného svazku clusteru a přidejte do ní informace o serveru.

### <a name="download-the-template"></a>Stažení šablony

1. V **Azure Migrate cíle migrace**  >  **Servers**  >  **Azure Migrate: Server Assessment**vyberte **Vyhledat**.
2. V možnosti **zjistit počítače**vyberte **importovat pomocí CSV**.
3. Vyberte **Stáhnout** a stáhněte šablonu sdíleného svazku clusteru. Případně si ho můžete [stáhnout přímo](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Stáhnout šablonu CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Přidat informace o serveru

Shromážděte data serveru a přidejte je do souboru CSV.

- Pokud chcete shromažďovat data, můžete je exportovat z nástrojů, které používáte pro správu místního serveru, jako je například VMware vSphere nebo vaše databáze správy konfigurace (CMDB).
- Pokud chcete zkontrolovat ukázková data, Stáhněte si náš [ukázkový soubor](https://go.microsoft.com/fwlink/?linkid=2108405).

Následující tabulka shrnuje pole souborů k vyplnění:

**Název pole** | **Závaznou** | **Podrobnosti**
--- | --- | ---
**Název serveru** | Ano | Doporučujeme zadat plně kvalifikovaný název domény (FQDN).
**IP adresa** | Ne | Adresa serveru.
**Cores** | Ano | Počet jader procesoru přidělených serveru.
**Memory (Paměť)** | Ano | Celková velikost paměti RAM (v MB) přidělená serveru.
**Název operačního systému** | Ano | Serverový operační systém. <br/> Vyhodnocování rozpoznávají názvy operačních systémů, které odpovídají nebo obsahují názvy v [tomto](#supported-operating-system-names) seznamu.
**Verze operačního systému** | Ne | Verze operačního systému serveru.
**Architektura operačního systému** | Ne | Architektura operačního systému serveru <br/> Platné hodnoty jsou: x64, x86, AMD64, 32-bit nebo 64-bit
**Počet disků** | Ne | Není nutné, pokud jsou k dispozici podrobnosti o jednotlivých discích.
**Velikost disku 1**  | Ne | Maximální velikost disku (v GB)<br/>[Přidáním sloupců](#add-multiple-disks) do šablony můžete přidat podrobnosti o dalších discích. Můžete přidat až osm disků.
**Disk 1 operace čtení** | Ne | Operace čtení z disku za sekundu
**Operace zápisu na disk 1** | Ne | Operace zápisu na disk za sekundu
**Propustnost čtení disku 1** | Ne | Data načtená z disku za sekundu, v MB za sekundu.
**Propustnost zápisu disku 1** | Ne | Data zapsaná na disk za sekundu, v MB za sekundu.
**Procento využití procesoru** | Ne | Procento využitého procesoru
**Procento využití paměti** | Ne | Procento využité paměti RAM
**Operace čtení z celkového počtu disků** | Ne | Operace čtení disku za sekundu
**Operace zápisu z celkového počtu disků** | Ne | Operace zápisu na disk za sekundu
**Propustnost čtení celkem disků** | Ne | Data načtená z disku v MB za sekundu.
**Propustnost zápisu celkem disků** | Ne | Data zapsaná na disk v MB za sekundu.
**Síť v propustnosti** | Ne | Data přijatá serverem v MB za sekundu.
**Propustnost sítě** | Ne | Data přenášená serverem v MB za sekundu.
**Typ firmwaru** | Ne | Firmware serveru. Hodnoty mohou být "BIOS" nebo "UEFI".
**Adresa MAC**| Ne | Adresa MAC serveru.


### <a name="add-operating-systems"></a>Přidat operační systémy

Posouzení rozpoznává konkrétní názvy operačních systémů. Libovolný název, který zadáte, musí přesně odpovídat jednomu z řetězců v [seznamu podporovaných názvů](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Přidat více disků

Šablona poskytuje výchozí pole pro první disk. Podobné sloupce můžete přidat až na osm disků.

Pokud například chcete zadat všechna pole pro druhý disk, přidejte tyto sloupce:

- Velikost disku 2
- Operace čtení disku 2
- Operace zápisu na disk 2
- Propustnost čtení disku 2
- Propustnost zápisu disku 2


## <a name="import-the-server-information"></a>Importovat informace o serveru

Po přidání informací do šablony sdíleného svazku clusteru importujte servery do vyhodnocování serveru.

1. V Azure Migrate v části **zjišťování počítačů**přejít na dokončenou šablonu.
2. Vyberte **Importovat**.
3. Zobrazí se stav importu.
    - Pokud se ve stavu zobrazí upozornění, můžete je buď opravit, nebo pokračovat bez jejich adresování.
    - Pro zlepšení přesnosti hodnocení Vylepšete informace o serveru, jak je navrženo v části upozornění.
    - Chcete-li zobrazit a opravit upozornění, vyberte možnost **Stáhnout podrobnosti upozornění. Sdílený svazek clusteru**. Tato operace stáhne sdílený svazek clusteru s upozorněními, která jsou součástí. Přečtěte si upozornění a opravte problémy podle potřeby.
    - Pokud se ve stavu objeví chyby, takže se stav importu **nezdařil**, je nutné tyto chyby opravit, aby bylo možné pokračovat v importu:
        1. Stáhněte si sdílený svazek clusteru, který teď obsahuje podrobnosti o chybě.
        1. Zkontrolujte a podle potřeby vyřešte chyby. 
        1. Znovu nahrajte změněný soubor.
4. Po **dokončení**importu se informace o serveru naimportovaly.

## <a name="update-server-information"></a>Aktualizovat informace o serveru

Informace o serveru můžete aktualizovat tak, že znovu naimportujete data pro server se stejným **názvem serveru**. Pole **název serveru** nemůžete změnit. Odstraňování serverů se v tuto chvíli nepodporuje.

## <a name="verify-servers-in-the-portal"></a>Ověřit servery na portálu

Ověření, že se servery zobrazí v Azure Portal po zjištění:

1. Otevřete řídicí panel Azure Migrate.
2. Na stránce **Azure Migrate-servery**  >  **Azure Migrate: posouzení serveru** vyberte ikonu, která zobrazuje počet **zjištěných serverů**.
3. Vyberte kartu **Import na základě** .



## <a name="supported-operating-system-names"></a>Podporované názvy operačních systémů

Názvy operačních systémů, které jsou k dispozici ve sdíleném svazku clusteru, musí obsahovat a odpovídat. Pokud ne, nebudete je moci vyhodnotit. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>systémem<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity systémy eComStation 1<br/>Serenity systémy eComStation <br/>Systém Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Systém Windows 3<br/>Windows 7<br/>Windows 8<br/>Systém Windows 95<br/>Windows 98<br/>Systém Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Prahová hodnota pro Windows Server<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření projektu Azure Migrate 
> * Zjištěné servery s použitím importovaného souboru CSV. Nyní spusťte posouzení [migrace virtuálních počítačů VMware do virtuálních počítačů Azure](tutorial-assess-vmware.md).