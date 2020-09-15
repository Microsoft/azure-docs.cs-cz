---
title: Zjišťování virtuálních počítačů Hyper-V pomocí serveru Azure Migrate Assessment
description: Naučte se zjišťovat místní virtuální počítače Hyper-V pomocí nástroje Azure Migrate Server Assessment Tool.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: fdf96b5767b461953fa88923aaa5050aff4613bc
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064240"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Kurz: zjišťování virtuálních počítačů Hyper-V pomocí posouzení serveru

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení. 

V tomto kurzu se dozvíte, jak zjistit místní virtuální počítače Hyper-V pomocí nástroje Azure Migrate: Nástroj pro vyhodnocení serveru s využitím odlehčeného Azure Migrateho zařízení. Zařízení nasadíte jako virtuální počítač Hyper-V, abyste mohli průběžně zjišťovat metadata počítačů a výkonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení účtu Azure
> * Připravte prostředí Hyper-V pro zjišťování.
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
**Hostitel Hyper-V** | Hostitelé Hyper-V, na kterých jsou virtuální počítače umístěné, můžou být samostatné nebo v clusteru.<br/><br/> V hostiteli musí být spuštěný systém Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2.<br/><br/> Ověřte, že jsou povolená příchozí připojení na portu WinRM 5985 (HTTP), aby se zařízení mohlo připojit k vyžádanému metadatům virtuálních počítačů a datům výkonu pomocí model CIM (Common Information Model) (CIM) relace.
**Nasazení zařízení** | vCenter Server potřebuje prostředky k přidělení virtuálního počítače pro zařízení:<br/><br/> – Windows Server 2016<br/><br/> -32 GB paměti RAM<br/><br/> – Osm vCPU<br/><br/> – Přibližně 80 GB diskového úložiště.<br/><br/> – Externí virtuální přepínač.<br/><br/> – Přístup k Internetu na virtuálním počítači přímo nebo prostřednictvím proxy serveru.
**Virtuální počítače** | Virtuální počítače můžou běžet s operačním systémem Windows nebo Linux. 

Než začnete, můžete [zkontrolovat data](migrate-appliance.md#collected-data---hyper-v) , která zařízení shromáždí během zjišťování.

## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s tímto:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory.

Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:


1. V Azure Portal vyhledejte "předplatná" a v části **služby**vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup**vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role**klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. V části **Přidat přiřazení role**vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-hyper-v/assign-role.png)

7. Na portálu vyhledejte uživatele a v části **služby**vyberte **Uživatelé**.
8. V **nastavení uživatele**ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-hyper-v/register-apps.png)

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů technologie Hyper-V

Nastavte účet s přístupem správce na hostitelích Hyper-V. Zařízení používá tento účet ke zjišťování.

- Možnost 1: Připravte účet s přístupem správce k hostitelskému počítači Hyper-V.
- Možnost 2: Připravte účet místního správce nebo účet správce domény a přidejte tento účet do těchto skupin: Uživatelé vzdálené správy, Správci technologie Hyper-V a uživatelé nástroje Performance Monitor.


## <a name="set-up-a-project"></a>Nastavení projektu

Sestavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu**vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt**vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-hyper-v/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí.

**Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>Nastavení zařízení

V tomto kurzu se nastavuje zařízení na virtuálním počítači s technologií Hyper-V.
- Stáhněte si šablonu zařízení a importujte ji do vCenter Server k vytvoření virtuálního počítače zařízení.
- Po vytvoření zařízení ho nastavte poprvé a zaregistrujte ho v Azure Migrate projektu.

> [!NOTE]
> Pokud z nějakého důvodu nemůžete zařízení nastavit pomocí šablony, můžete ho nastavit pomocí skriptu PowerShellu. [Přečtěte si další informace](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="download-the-vhd"></a>Stažení virtuálního pevného disku

1. V **Azure Migrate cíle migrace**  >  **Servers**  >  **Azure Migrate: Server Assessment**vyberte **Vyhledat**.
2. V rozevíracích **seznamech počítačů**  >  , ve**kterých jsou počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3. Vyberte **Stáhnout** a Stáhněte soubor VHD.

   ![Výběry pro stažení souboru vajíček](./media/tutorial-discover-hyper-v/download-vhd.png)



### <a name="deploy-the-appliance-vm"></a>Nasazení virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač:

1. Stáhněte soubor VHD s příponou ZIP na hostitele Hyper-V, na kterém bude virtuální počítač zařízení umístěný.
2. Extrahujte soubor zip.

    - V extrahovaném umístění se soubor extrahuje do složky s názvem AzureMigrateAppliance_VersionNumber.
    - Tato složka obsahuje podsložku, která se také označuje jako AzureMigrateAppliance_VersionNumber.
    - Tato podsložka obsahuje tři další podsložky – snímky, virtuální pevné disky a Virtual Machines.

3. Spusťte Správce technologie Hyper-V. V nabídce **Akce**klikněte na **importovat virtuální počítač**.

    ![Položka nabídky ve Správci technologie Hyper-V pro import VHD](./media/tutorial-discover-hyper-v/deploy-vhd.png)

4. V Průvodci importem virtuálního počítače > **než začnete**, klikněte na **Další**.
5. V části **najít složku**vyberte složku **Virtual Machines** . Potom klikněte na **Další**.
6. V nabídce **Vybrat virtuální počítač**klikněte na **Další**.
7. V části **zvolit typ importu**klikněte na **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)**. Potom klikněte na **Další**.
8. V části **zvolit cíl**ponechte výchozí nastavení. Klikněte na **Next** (Další).
9. V části **složky úložiště**ponechte výchozí nastavení. Klikněte na **Next** (Další).
10. V části **zvolit síť**zadejte virtuální přepínač, který bude virtuální počítač používat. Přepínač potřebuje připojení k Internetu, aby bylo možné odesílat data do Azure. [Přečtěte si informace](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) o vytvoření virtuálního přepínače.
11. V části **Souhrn**zkontrolujte nastavení. Klikněte na **Dokončit**.
12. Ve Správci technologie Hyper-V > **Virtual Machines**spusťte virtuální počítač.


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ověřte přístup k zařízení následujícím způsobem:

1. Ověřte, že se virtuální počítač může připojit k Azure.
    - Ve veřejném cloudu by počítač zařízení měl být schopný se připojit k těmto [adresám URL](migrate-appliance.md#public-cloud-urls).
    - V cloudu pro státní správu by se zařízení mělo umět připojit k těmto [adresám URL státní správy](migrate-appliance.md#government-cloud-urls).
2. Ujistěte se, že jsou tyto porty otevřené na počítači zařízení:

    - Pokud chcete povolit připojení ke vzdálené ploše zařízení, povolte příchozí připojení na portu TCP 3389.
    - Povolte příchozí připojení na portu 44368 pro vzdálený přístup k webové aplikaci v zařízení pomocí adresy URL: https:// \<appliance-ip-or-name> : 44368.
    - Povolí odchozí připojení na portu 443 (HTTPS), aby se odesílaly metadata zjišťování a výkonu Azure Migrate.


### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto staženého virtuálního pevného disku, první dva kroky v tomto postupu se netýkají.

1. Ve Správci technologie Hyper-V > **Virtual Machines**klikněte pravým tlačítkem na virtuální počítač > **připojit**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
1. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
    - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
      - Klikněte na **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN .
      - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
      - Podporuje se jen proxy protokolu HTTP.
    - **Časová synchronizace**: čas je ověřený. Čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování virtuálních počítačů fungovalo správně.
    - **Instalovat aktualizace**: posouzení Azure Migrate serveru kontroluje, jestli má zařízení nainstalované nejnovější aktualizace.

### <a name="register-the-appliance"></a>Registrace zařízení 

1. V **Azure Migrate registrovat v**vyberte **Přihlásit**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.

    ![Kliknutím na přihlašovací jméno spusťte registraci zařízení.](./media/tutorial-discover-hyper-v/register.png)

1. Na **přihlašovací** stránce se přihlaste pomocí uživatelského jména a hesla Azure. Přihlášení pomocí PIN kódu se nepodporuje.

    ![Tlačítko přihlásit k registraci zařízení](./media/tutorial-discover-hyper-v/sign-in.png)
1. Po úspěšném přihlášení se vraťte do aplikace.
1. V části **zaregistrovat v Azure Migrate**vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt, a pak vyberte projekt.
1. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
3. Vyberte **Zaregistrovat**. Pak klikněte na **pokračovat**. Zpráva zobrazuje registraci jako úspěšnou.

    ![Vyplňte předplatné, projekt a název zařízení a zaregistrujte zařízení.](./media/tutorial-discover-hyper-v/success-register.png)


### <a name="delegate-credentials-for-smb-vhds"></a>Pověření delegáta pro virtuální pevné disky SMB

Pokud používáte na SMB virtuální pevné disky, musíte povolit delegování přihlašovacích údajů ze zařízení na hostitele Hyper-V. K tomu je potřeba povolit, aby každý hostitel fungoval jako delegát pro zařízení. Pokud jste postupovali podle kurzů v předchozím kurzu, provedli jste to v předchozím kurzu, když jste připravili technologii Hyper-V pro účely posouzení a migrace. Měli byste buď nastavit CredSSP pro hostitele [ručně](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials), nebo [Spustit skript](tutorial-prepare-hyper-v.md#run-the-script) , který to dělá.

Povolit na zařízení následujícím způsobem:

#### <a name="option-1"></a>Možnost 1

Na virtuálním počítači zařízení spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou příklady názvů hostitelů.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Příklad: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Možnost 2

Případně to udělejte v Editor místních zásad skupiny na zařízení:

1. V konfiguraci počítače **Zásady místního počítače**  >  **Computer Configuration**klikněte na **šablony pro správu**  >  **System**  >  **delegování přihlašovacích údajů**systému.
2. Dvakrát klikněte na **Povolit delegování nových přihlašovacích údajů**a vyberte **povoleno**.
3. V nabídce **Možnosti**klikněte na **Zobrazit**a do seznamu přidejte každého hostitele Hyper-V, který chcete zjistit, a použijte příkaz **WSMan/** jako předponu.
4. Pak v **delegování přihlašovacích údajů**dvakrát klikněte na možnost **umožňuje delegovat nové přihlašovací údaje pomocí ověřování serveru jenom s protokolem NTLM**. Znovu přidejte všechny hostitele Hyper-V, které chcete vyhledat, do seznamu s použitím nástroje **WSMan/** jako předpony.



## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Zařízení se musí připojit k hostitelům Hyper-V a zjistit virtuální počítače.


### <a name="connect-to-hyper-v-hosts"></a>Připojení k hostitelům Hyper-V

1. Do pole **uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu, které zařízení použije ke zjištění virtuálních počítačů. Zadejte popisný název přihlašovacích údajů a klikněte na **Uložit podrobnosti**.
2. Klikněte na **Přidat hostitele**a zadejte podrobnosti o hostiteli nebo clusteru Hyper-V.
3. Klikněte na **Validate** (Ověřit). Po ověření se zobrazí počet virtuálních počítačů, které se dají zjistit u každého hostitele nebo clusteru.
    - Pokud se ověření pro hostitele nepovede, přečtěte si chybu, když najedete myší na ikonu ve sloupci **stav** . Opravte problémy a znovu ověřte.
    - Chcete-li odebrat hostitele nebo clustery, vyberte možnost > **Odstranit**.
    - Z clusteru nelze odebrat konkrétního hostitele. Můžete odebrat jenom celý cluster.
    - Cluster můžete přidat i v případě, že v clusteru dojde k problémům s konkrétními hostiteli.
4. Po ověření klikněte na **Uložit a spusťte zjišťování a** spusťte proces zjišťování.

Spustí se zjišťování. Metadata zjištěných serverů, které se mají zobrazit v Azure Portal, trvá přibližně 1,5 minut na hostitele.


### <a name="verify-discovered-vms-in-the-portal"></a>Ověřit zjištěné virtuální počítače na portálu

Po zjištění můžete ověřit, že se virtuální počítače zobrazují v Azure Portal:

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: vyhodnocování serveru**vyberte ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---hyper-v) , která zařízení shromažďuje během zjišťování.