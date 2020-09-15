---
title: Zjištění instancí AWS pomocí posouzení serveru Azure Migrate
description: Naučte se zjišťovat instance AWS pomocí posouzení serveru Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 6924182e6425b0927583e8c6498c7073c4ff1806
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064257"
---
# <a name="tutorial-discover-aws-instances-with-server-assessment"></a>Kurz: zjištění instancí AWS pomocí posouzení serveru

Jako součást cesty migrace do Azure zjistíte, že vaše servery jsou vyhodnoceny a migrace.

V tomto kurzu se dozvíte, jak zjistit instance Amazon Web Services (AWS) pomocí nástroje Azure Migrate: Server Assessment Tool s použitím odlehčeného zařízení Azure Migrate. Zařízení nasadíte jako fyzický server, abyste mohli průběžně zjišťovat metadata o počítači a výkonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte účet Azure.
> * Připravte instance AWS pro zjišťování.
> * Jak vytvořit projekt Azure Migrate.
> * Nastavte zařízení Azure Migrate.
> * Spusťte nepřetržité zjišťování.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti.  

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, Projděte si tyto požadavky.

**Požadavek** | **Podrobnosti**
--- | ---
**Náplně** | Potřebujete virtuální počítač EC2, na kterém chcete spustit zařízení Azure Migrate. Počítač by měl mít:<br/><br/> – Nainstalovaný systém Windows Server 2016. Spuštění zařízení na počítači s Windows serverem 2019 se nepodporuje.<br/><br/> – 16 GB RAM, 8 vCPU, přibližně 80 GB diskového úložiště a externí virtuální přepínač.<br/><br/> – Statická nebo dynamická IP adresa s přístupem k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.
**Instance systému Windows** | Povolí příchozí připojení na portu WinRM 5985 (HTTP), takže zařízení může vyžádat metadata o konfiguraci a výkonu.
**Instance systému Linux** | Povolí příchozí připojení na portu 22 (TCP).

## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s tímto:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory.

Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby**vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup**vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role**klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-aws/azure-account-access.png)

6. V části **Přidat přiřazení role**vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-aws/assign-role.png)

7. Na portálu vyhledejte uživatele a v části **služby**vyberte **Uživatelé**.
8. V **nastavení uživatele**ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-aws/register-apps.png)


## <a name="prepare-aws-instances"></a>Příprava instancí AWS

Nastavte účet, který může zařízení použít pro přístup k AWS instancím.

- Pro Windows servery nastavte místní uživatelský účet na všech serverech Windows, které chcete zahrnout do zjišťování. Přidejte uživatelský účet do následujících skupin:-Remote Management Users-Performance Monitor Users-Performance Log Users.
 - V případě serverů s Linuxem musíte na serverech s Linuxem, které chcete vyhledat, mít účet superuživatele.


## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu**vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt**vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-aws/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí.

**Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-aws/added-tool.png)

## <a name="set-up-the-appliance"></a>Nastavení zařízení

Pokud chcete zařízení nastavit na instanci AWS, Stáhněte a spusťte skript instalačního programu na [vhodné instanci](#prerequisites). Po vytvoření zařízení zkontrolujete, že se může připojit k Azure, nakonfigurovat ho poprvé a zaregistrovat ho v Azure Migrate projektu.

### <a name="download-the-installer-script"></a>Stažení instalačního skriptu

Stáhněte si soubor zip pro zařízení.

1. V Azure Migrate **cíle migrace**  >  na**servery**  >  **: vyhodnocování serveru**klikněte na **zjistit**.
2. V nabídce **zjistit**počítače  >  **jsou vaše počítače virtualizované?** klikněte na **nevirtualizované/jiné**.
3. Kliknutím na **Stáhnout** Stáhněte soubor zip.



### <a name="install-the-appliance"></a>Instalace zařízení

Skript instalačního programu provede následující akce:

- Nainstaluje agenty a webovou aplikaci pro zjišťování AWS a posouzení.
- Nainstalujte role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstalujte zapisovatelný modul IIS. [Přečtěte si další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) o trvalé podrobnosti nastavení pro Azure Migrate.
- Vytvoří následující soubory pod cestou:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spusťte skript následujícím způsobem:

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení.  Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
2. Na výše uvedeném serveru s oprávněním správce (zvýšené) spusťte PowerShell.
3. Změňte adresář PowerShellu na složku, do které byl obsah extrahován ze staženého souboru ZIP.
4. Spusťte skript s názvem **AzureMigrateInstaller.ps1** spuštěním následujícího příkazu:

    - Pro veřejný cloud: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Pro Azure Government: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skript spustí webovou aplikaci zařízení po úspěšném dokončení.

Pokud přecházíte mezi všemi problémy, získáte přístup k protokolům skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log pro řešení potíží.


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ověřte přístup k zařízení následujícím způsobem:

1. Ujistěte se, že počítač zařízení se může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .
2. Ujistěte se, že jsou tyto porty otevřené na počítači zařízení:

    - Pokud chcete povolit připojení ke vzdálené ploše zařízení, povolte příchozí připojení na portu TCP 3389.
    - Povolit příchozí připojení na portu 44368 pro vzdálený přístup k webové aplikaci zařízení pomocí adresy URL: https://<zařízení-IP-nebo-Name>:44368.
    - Povolí odchozí připojení na portu 443 (HTTPS), aby se odesílaly metadata zjišťování a výkonu Azure Migrate.


### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení tak, že vyberete zástupce aplikace.
2. Ve webové aplikaci Azure Migrate zařízení > **nastavit požadavky**, přečtěte si nebo přijměte licenční podmínky a přečtěte si informace třetích stran.
3. Zařízení zkontroluje, jestli má virtuální počítač přístup k Internetu, a jestli je čas na virtuálním počítači synchronizovaný s internetovým časem.
    - Pokud používáte proxy server, klikněte na **nastavit proxy** a zadejte adresu a port proxy serveru (ve formátu http://ProxyIPAddress nebo http://ProxyFQDN) . 
    - Zadejte přihlašovací údaje, pokud proxy server vyžaduje ověření. Podporuje se jen proxy protokolu HTTP.
4. Zařízení nainstaluje nejnovější aktualizace Azure Migrate.
5. Zkontrolujte nastavení a klikněte na **pokračovat**.

       

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. V **Azure Migrate registrovat v**vyberte **Přihlásit**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.

    ![Kliknutím na přihlašovací jméno spusťte registraci zařízení.](./media/tutorial-discover-aws/register.png)

1. Na **přihlašovací** stránce se přihlaste pomocí uživatelského jména a hesla Azure. Přihlášení pomocí PIN kódu se nepodporuje.

    ![Tlačítko přihlásit k registraci zařízení](./media/tutorial-discover-aws/sign-in.png)
1. Po úspěšném přihlášení se vraťte do aplikace.
1. V části **zaregistrovat v Azure Migrate**vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt, a pak vyberte projekt.
1. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
3. Vyberte **Zaregistrovat**. Pak klikněte na **pokračovat**. Zpráva zobrazuje registraci jako úspěšnou.

    ![Vyplňte předplatné, projekt a název zařízení a zaregistrujte zařízení.](./media/tutorial-discover-aws/success-register.png)


## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Zařízení se připojí k instanci AWS pro zjišťování.

1. Klikněte na **Přidat přihlašovací údaje** a zadejte přihlašovací údaje účtu, které zařízení použije k zjišťování serverů.  
2. Přihlaste se pomocí uživatelského jména a hesla. Přihlášení pomocí klíče se nepodporuje. Uživatel musí být také kořenovým přihlášením nebo součástí místní skupiny správců.
3. Zadejte **operační systém**, popisný název přihlašovacích údajů a uživatelské jméno a heslo. Pak klikněte na **Přidat**.
Můžete přidat několik přihlašovacích údajů pro servery se systémem Windows a Linux.
4. Klikněte na **Přidat server**a zadejte podrobnosti o serveru – plně kvalifikovaný název domény/IP adresa a popisný název přihlašovacích údajů (jedna položka na řádek) pro připojení k serveru.
5. Klikněte na **Validate** (Ověřit). Po ověření se zobrazí seznam serverů, které se dají zjistit.
    - Pokud se ověření serveru nepovede, zkontrolujte chybu přesunutím ukazatele myši na ikonu ve sloupci **stav** . Opravte problémy a znovu ověřte.
    - Pokud chcete odebrat server, vyberte > **Odstranit**.
6. Po ověření klikněte na **Uložit a spusťte zjišťování a** spusťte proces zjišťování.

Spustí se zjišťování. Aby se metadata zobrazovala v Azure Portal, obvykle trvá méně než dvě minuty na server.


## <a name="verify-discovered-vms-in-the-portal"></a>Ověřit zjištěné virtuální počítače na portálu

Po zjištění můžete ověřit, že se virtuální počítače zobrazují v Azure Portal:

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: vyhodnocování serveru**vyberte ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="next-steps"></a>Další kroky

- [Posuzuje fyzické servery](tutorial-migrate-aws-virtual-machines.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---physical) , která zařízení shromažďuje během zjišťování.
