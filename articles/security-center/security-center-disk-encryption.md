---
title: Šifrování virtuálního počítače Azure | Dokumentace Microsoftu
description: Tento dokument vám pomůže zašifrovat virtuální počítač Azure, pokud dostanete výstrahu od služby Azure Security Center.
services: security, security-center
documentationcenter: na
author: TomShinder
manager: mbaldwin
editor: ''
ms.assetid: f6c28bc4-1f79-4352-89d0-03659b2fa2f5
ms.service: security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomsh
ms.openlocfilehash: bde17a47e0e3e70daf52f4c460118c054b7c1152
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="encrypt-an-azure-virtual-machine"></a>Šifrování virtuálního počítače Azure
Azure Security Center vás upozorní, pokud máte virtuální počítače, které nejsou šifrované. Tyto výstrahy se zobrazují jako upozornění s vysokou závažností. Doporučuje se tyto virtuální počítače zašifrovat.

![Doporučení pro šifrování disku](./media/security-center-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Informace v tomto dokumentu se vztahují k šifrování virtuálních počítačů bez použití klíčového šifrovacího klíče (který se vyžaduje k zálohování virtuálních počítačů pomocí služby Azure Backup). Informace o použití klíčového šifrovacího klíče k zajištění podpory služby Azure Backup pro šifrované Azure Virtual Machines najdete v článku [Azure Disk Encryption pro Azure Virtual Machines s Windows a Linuxem](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).
>
>

Pokud chcete zašifrovat virtuální počítače Azure, u kterých služba Azure Security Center zjistila, že potřebují šifrování, doporučujeme použít následující postup:

* Instalace a konfigurace Azure Powershellu. Díky tomu bude možné spustit příkazy PowerShellu, které jsou nutné k instalaci požadovaných součástí pro šifrování Azure Virtual Machines.
* Získání a spuštění skriptu Azure PowerShellu pro Azure Disk Encryption
* Šifrování virtuálních počítačů

Cílem tohoto dokumentu je umožnit šifrování virtuálních počítačů i v případě, že nemáte s Azure PowerShellem žádné nebo téměř žádné zkušenosti.
Tento dokument předpokládá, že používáte Windows 10 jako klientský počítač, ze kterého budete konfigurovat Azure Disk Encryption.

Existuje celá řada přístupů, které lze využít pro instalaci požadovaných součástí a konfiguraci šifrování pro Azure Virtual Machines. Pokud jste už s Azure PowerShellem nebo rozhraním příkazového řádku Azure CLI dobře obeznámeni, dáte možná přednost alternativním přístupům.

> [!NOTE]
> Další informace o alternativních přístupech ke konfiguraci šifrování pro virtuální počítače Azure najdete v tématu [Azure Disk Encryption pro Azure Virtual Machines s Windows a Linuxem](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).
>
>

## <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Budete potřebovat, aby na vašem počítači byl nainstalovaný Azure PowerShell verze 1.2.1 nebo novější. Všechny kroky nutné k tomu, abyste ve svém počítači mohli pracovat s Azure PowerShellem, najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Nejjednodušší je využít instalaci Web PI uvedenou v tomto článku. Instalaci Web PI využijte, i když už máte Azure PowerShell nainstalovaný. Zajistíte tak, abyste měli jeho nejnovější verzi.

## <a name="obtain-and-run-the-azure-disk-encryption-prerequisites-configuration-script"></a>Získání a spuštění konfiguračního skriptu požadovaných součástí pro Azure Disk Encryption
Konfigurační skript požadovaných součástí pro Azure Disk Encryption nastaví všechny požadované součásti, které jsou potřeba pro šifrování Azure Virtual Machines.

1. Přejděte na stránku GitHubu, která obsahuje [skript pro instalaci požadovaných součástí pro Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
2. Na stránce GibHubu klikněte na tlačítko **Raw**.
3. Pomocí kombinace kláves **CTRL-A** vyberte veškerý text na této stránce a potom pomocí kombinace kláves **CTRL-C** zkopírujte tento text do schránky.
4. Otevřete **Poznámkový blok** a vložte zkopírovaný text do Poznámkového bloku.
5. Na jednotce C: vytvořte novou složku s názvem **AzureADEScript**.
6. Uložte soubor Poznámkového bloku – klikněte na **Soubor** a potom klikněte na **Uložit jako**. Do textového pole Název souboru zadejte **ADEPrereqScript.ps1** a klikněte na **Uložit**. (Nezapomeňte v názvu souboru použít uvozovky, jinak se soubor uloží s příponou .txt.)

Když je obsah skriptu uložený, otevřete skript v integrovaném skriptovacím prostředí v PowerShellu:

1. V nabídce Start klikněte na **Cortana**. Vyhledejte **Cortana** PowerShell zadáním řetězce **PowerShell** do textového pole Cortany pro vyhledávání.
2. Klikněte pravým tlačítkem na **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** a klikněte na **Spustit jako správce**.
3. V okně **Správce: Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** klikněte na **Zobrazit** a potom klikněte na **Zobrazit podokno skriptu**.
4. Pokud se na pravé straně tohoto okna zobrazí podokno **Příkazy**, zavřete ho kliknutím na **x** v pravém horním rohu. Pokud je text příliš malý, použijte **CTRL+Add** (Add je znak +). Pokud je text příliš velký, použijte **CTRL+Subtract** (Subtract je znak -).
5. Klikněte na **Soubor** a potom klikněte na **Otevřete**. Přejděte ke složce **C:\AzureADEScript** a poklikejte na **ADEPrereqScript**.
6. V integrovaném skriptovacím prostředí (ISE) v prostředí PowerShell by se měl zobrazit obsah souboru **ADEPrereqScript**. Jednotlivé komponenty, například příkazy, parametry a proměnné, jsou rozlišené barevně.

Měli byste vidět něco podobného jako na následujícím obrázku.

![Okno integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig2.png)

Horní podokno se označuje jako podokno skriptu a dolní podokno se označuje jako konzola. Tyto termíny využijeme dále v tomto článku.

## <a name="run-the-azure-disk-encryption-prerequisites-powershell-command"></a>Spuštění příkazu PowerShellu pro požadované součásti služby Azure Disk Encryption
Skript pro požadované součásti služby Azure Disk Encryption po spuštění zobrazí výzvu k zadání následujících informací:

* **Název skupiny prostředků** – 	Název skupiny prostředků, do kterého chcete vložit Key Vault.  Pokud skupina prostředků se zadaným názvem neexistuje, vytvoří se nová. Pokud už máte skupinu prostředků, kterou chcete použít v tomto předplatném, zadejte název této skupiny prostředků.
* **Název pro Key Vault** – Název služby Key Vault, do které se umístí šifrovací klíče. Pokud služba Key Vault se zadaným názvem neexistuje, vytvoří se nová. Pokud už máte službu Key Vault, kterou chcete použít, zadejte název této služby Key Vault.
* **Umístění** – Umístění služby Key Vault. Ověřte, že Key Vault a virtuální počítače, které se mají šifrovat, jsou ve stejném umístění. Pokud toto umístění neznáte, dále v tomto článku jsou uvedené kroky, které vám ukážou, jak toto umístění zjistit.
* **Název aplikace Azure Active Directory** – Název aplikace Azure Active Directory, která se použije k zápisu tajných klíčů do služby Key Vault. Pokud aplikace se zadaným názvem neexistuje, vytvoří se nová. Pokud už máte aplikaci Azure Active Directory, kterou chcete použít, zadejte název této aplikace Azure Active Directory.

> [!NOTE]
> Pokud vás zajímá, proč je potřeba vytvořit aplikaci Azure Active Directory, přečtěte si oddíl věnovaný *registraci aplikace ve službě Azure Active Directory* v článku [Začínáme se službou Azure Key Vault](../key-vault/key-vault-get-started.md).
>
>

Pomocí následujících kroků zašifrujte virtuální počítač Azure:

1. Pokud jste zavřeli Integrované skriptovací prostředí (ISE) v prostředí PowerShell, otevřete jeho instanci se zvýšenými oprávněními. Pokud Integrované skriptovací prostředí (ISE) v prostředí PowerShell ještě není spuštěné, postupujte podle pokynů uvedených výše v tomto článku. Pokud jste skript zavřeli, otevřete **ADEPrereqScript.ps1** kliknutím na **Soubor** a **Otevřít** a výběrem tohoto skriptu ve složce **c:\AzureADEScript**. Pokud jste tento článek procházeli od začátku, přejděte k následujícímu kroku.
2. V konzole Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell (dolní podokno Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell) nastavte zadáním **cd c:\AzureADEScript** fokus na umístění skriptu a stiskněte **ENTER**.
3. Nastavte na počítači zásady spouštění, aby bylo možné skript spustit. V konzole zadejte **Set-ExecutionPolicy Unrestricted** a stiskněte ENTER. Pokud se zobrazí dialogové okno s informacemi o důsledcích změny zásady spouštění, klikněte na **Ano všem** nebo **Ano** (pokud se zobrazí **Ano všem**, vyberte tuto možnost; pokud se nezobrazí **Ano všem**, klikněte na **Ano**).
4. Přihlaste se ke svému účtu Azure. V konzole zadejte **Connect-AzureRmAccount** a stiskněte **ENTER**. Zobrazí se dialogové okno pro zadání přihlašovacích údajů. (Zkontrolujte, že máte práva měnit virtuální počítače. Pokud tato práva nemáte, nebudete je moct zašifrovat. Pokud si nejste jisti, zeptejte se správce nebo vlastníka předplatného.) Měly by se zobrazit tyto informace: **prostředí**, **účet**, **ID tenanta**, **ID předplatného** a **aktuální účet úložiště**. Zkopírujte **ID předplatného** do Poznámkového bloku. Budete ho potřebovat v kroku 6.
5. Zjistěte, k jakému předplatnému patří váš virtuální počítač, a dál zjistěte jeho umístění. Přejděte na adresu [https://portal.azure.com](ttps://portal.azure.com) a přihlaste se.  Na levé straně stránky klikněte na **Virtual Machines**. Zobrazí se seznam vašich virtuálních počítačů a předplatná, ke kterým patří.

   ![Virtuální počítače](./media/security-center-disk-encryption/security-center-disk-encryption-fig3.png)
6. Vraťte se do Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell. Nastavte kontext předplatného, ve kterém skript poběží. V konzole zadejte **Select-AzureRmSubscription –SubscriptionId <ID_předplatného>** (místo **< ID_předplatného>** zadejte aktuální ID předplatného) a stiskněte **ENTER**. Zobrazí se tyto informace: prostředí, **účet**, **ID tenanta**, **ID předplatného** a **aktuální účet úložiště**.
7. Nyní jste připraveni skript spustit. Klikněte na tlačítko **Spustit skript** nebo stiskněte **F5** na klávesnici.

   ![Spuštění powershellového skriptu](./media/security-center-disk-encryption/security-center-disk-encryption-fig4.png)
8. Skript vyzve k zadání **resourceGroupName:** Zadejte název *skupiny prostředků*, kterou chcete použít, a stiskněte **ENTER**. Pokud ji nemáte, zadejte název, pod kterým ji chcete vytvořit. Pokud už máte *skupinu prostředků*, kterou chcete použít (například skupinu, ve které je váš virtuální počítač), zadejte název této skupiny prostředků.
9. Skript vyzve k zadání **keyVaultName:** Zadejte název služby *Key Vault*, kterou chcete použít, a stiskněte ENTER. Pokud ji nemáte, zadejte název, pod kterým ji chcete vytvořit. Pokud už máte službu Key Vault, kterou chcete použít, zadejte název této služby *Key Vault*.
10. Skript vyzve k zadání **umístění:** zadejte název umístění, ve kterém je virtuální počítač, který chcete zašifrovat, a stiskněte **ENTER**. Pokud si umístění nepamatujete, přejděte zpátky ke kroku 5.
11. Skript vyzve k zadání **aadAppName:** Zadejte název aplikace *Azure Active Directory*, kterou chcete použít, a stiskněte **ENTER**. Pokud ji nemáte, zadejte název, pod kterým ji chcete vytvořit. Pokud už máte *aplikaci Azure Active Directory*, kterou chcete použít, zadejte název této *aplikace Azure Active Directory*.
12. Zobrazí se přihlašovací dialogové okno. Zadejte svoje přihlašovací údaje (ano, už jste se přihlásili, ale teď to musíte udělat znova).
13. Skript se spustí a po jeho dokončení se zobrazí výzva ke zkopírování hodnot **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl** a **keyVaultResourceId**. Zkopírujte všechny tyto hodnoty do schránky a pak je vložte do Poznámkového bloku.
14. Vraťte se do Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell, nastavte kurzor na konec posledního řádku a stiskněte **ENTER**.

Výstup skriptu by měl vypadat podobně jako na následujícím obrázku:

![Výstup prostředí PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig5.png)

## <a name="encrypt-the-azure-virtual-machine"></a>Šifrování virtuálního počítače Azure
Nyní jste připraveni k šifrování virtuálního počítače. Pokud je virtuální počítač umístěný ve stejné skupině prostředků jako služba Key Vault, můžete přejít ke krokům pro šifrování. Pokud ale virtuální počítač není ve stejné skupině prostředků jako služba Key Vault, budete muset v konzole Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell zadat následující:

**$resourceGroupName = &lt;’skupina_prostředků_virtuálního_počítače’&gt;**

Místo **<’skupina_prostředků_virtuálního_počítače’>** použijte název skupiny prostředků, která obsahuje váš virtuální počítač (v jednoduchých uvozovkách). Potom stiskněte **ENTER**.
Pro potvrzení, že byla zadaná správná skupina prostředků, zadejte v konzole Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell následující text:

**$resourceGroupName**

Stiskněte **ENTER**. Měl by se zobrazit název skupiny prostředků, ve které jsou umístěné vaše virtuální počítače. Příklad:

![Výstup prostředí PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig6.png)

### <a name="encryption-steps"></a>Kroky pro šifrování
Nejdřív musíte PowerShellu předat název virtuálního počítače, který chcete zašifrovat. V konzole zadejte:

**$vmName = &lt;’název_virtuálního_počítače’&gt;**

Místo **<’název_virtuálního_počítače’>** zadejte název vašeho virtuálního počítače (v jednoduchých uvozovkách) a potom stiskněte **ENTER**.

K potvrzení, že byl zadaný správný název virtuálního počítače, zadejte:

**$vmName**

Stiskněte **ENTER**. Měl by se zobrazit název virtuálního počítače, který chcete zašifrovat. Příklad:

![Výstup prostředí PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig7.png)

Existují dva způsoby, kterými je možné spustit šifrovací příkazy pro zašifrování všech jednotek virtuálního počítače. Prvním způsobem je zadat v konzole Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell následující příkaz:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType All
~~~

Po zadání tohoto příkazu stiskněte **ENTER**.

Druhý způsob spočívá v tom, že kliknete v podokně skriptu (horní podokno Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell) a posunete se dolů na konec skriptu. Zvýrazněte příkaz uvedený výše, klikněte na něj pravým tlačítkem a potom klikněte na **Spustit výběr** nebo stiskněte **F8** na klávesnici.

![Integrované skriptovací prostředí (ISE) v prostředí PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig8.png)

Bz ohledu na způsob, který použijete, se zobrazí dialogové okno informující o tom, že dokončení této operace bude trvat 10 až 15 minut. Klikněte na **Ano**.

V průběhu šifrování se můžete vrátit na web Azure Portal a prohlédnout si stav virtuálního počítače. Na levé straně stránky klikněte na **Virtual Machines** a potom v okně **Virtual Machines** klikněte na název virtuálního počítače, který šifrujete. V zobrazeném okně uvidíte, že jako **stav** se uvádí **aktualizace**. To znamená, že probíhá šifrování.

![Další podrobnosti o virtuálním počítači](./media/security-center-disk-encryption/security-center-disk-encryption-fig9.png)

Vraťte se do Integrovaného skriptovacího prostředí (ISE) v prostředí PowerShell. Po dokončení skriptu uvidíte to, co je na obrázku výš.

![Výstup prostředí PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig10.png)

Abyste se přesvědčili, že virtuální počítač je zašifrovaný, vraťte se na web Azure Portal a klikněte na **Virtual Machines** na levé straně stránky. Klikněte na název virtuálního počítače, který jste zašifrovali. V okně **Nastavení** klikněte na **Disky**.

![Možnosti nastavení](./media/security-center-disk-encryption/security-center-disk-encryption-fig11.png)

V okně **Disky** uvidíte, že **šifrování** je **povolené**.

![Vlastnosti disku](./media/security-center-disk-encryption/security-center-disk-encryption-fig12.png)

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili zašifrovat virtuální počítač Azure. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
