---
title: Zásady bezpečného a používání
description: Přečtěte si o zabezpečení a zásadách pro virtuální počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 840045da33938d4c1cd725fd5a99bf1b8014f6b1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748461"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Zásady zabezpečení a používání virtuálních počítačů v Azure

Pro aplikace, které spouštíte, je důležité udržovat svůj virtuální počítač zabezpečený. Zabezpečení virtuálních počítačů může zahrnovat jednu nebo víc služeb Azure a funkcí, které pokrývají zabezpečený přístup k virtuálním počítačům a zabezpečené úložiště vašich dat. Tento článek poskytuje informace, které vám umožní zajistit zabezpečení virtuálních počítačů a aplikací.

## <a name="antimalware"></a>antimalware

Moderní hrozby pro cloudová prostředí jsou dynamické a zvyšují tlak na zachování efektivní ochrany, aby splňoval požadavky na dodržování předpisů a zabezpečení. [Microsoft Antimalware pro Azure](../security/fundamentals/antimalware.md) je bezplatná funkce ochrany v reálném čase, která pomáhá identifikovat a odebírat viry, spyware a další škodlivý software. Výstrahy je možné nakonfigurovat tak, aby vás upozornily na to, že se známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na vašem VIRTUÁLNÍm počítači. Nepodporuje se na virtuálních počítačích se systémem Linux nebo Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) pomáhá zabránit, zjišťovat a reagovat na hrozby pro vaše virtuální počítače. Security Center poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure, pomáhá detekovat hrozby, které by jinak neinformovaly, a spolupracuje s širokou ekosystémem řešení zabezpečení.

Přístup za běhu Security Center můžete v rámci nasazení virtuálního počítače použít k uzamknutí příchozího provozu na virtuální počítače Azure. tím se sníží riziko útoků na útoky a v případě potřeby získáte snadný přístup k virtuálním počítačům. Když je povolený za běhu a uživatel požaduje přístup k virtuálnímu počítači, Security Center zkontroluje, jaká oprávnění uživatel má pro daný virtuální počítač. Pokud mají správná oprávnění, požadavek se schválí a Security Center automaticky nakonfiguruje skupiny zabezpečení sítě (skupin zabezpečení sítě) tak, aby umožňovaly příchozí přenosy na vybrané porty po omezené množství času. Po vypršení časového limitu Security Center obnoví skupin zabezpečení sítě do jejich předchozích stavů. 

## <a name="encryption"></a>Šifrování

K dispozici jsou dvě metody šifrování pro spravované disky. Šifrování na úrovni operačního systému, které je Azure Disk Encryption a šifrování na úrovni platformy, což je šifrování na straně serveru.

### <a name="server-side-encryption"></a>Šifrování na straně serveru

Služba Azure Managed disks automaticky šifruje vaše data ve výchozím nastavení při uchování do cloudu. Šifrování na straně serveru chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. Data ve službě Azure Managed disks jsou transparentně šifrovaná pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejúčinnějších šifrovacích šifr, která jsou kompatibilní se standardem FIPS 140-2.

Šifrování nemá vliv na výkon spravovaných disků. Pro šifrování se neúčtují žádné další náklady.

Pro šifrování spravovaného disku můžete spoléhat na klíče spravované platformou, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem* , který se použije k šifrování a dešifrování všech dat ve službě Managed disks. 

Další informace o šifrování na straně serveru najdete v článcích pro [Windows](./disk-encryption.md) nebo [Linux](./disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Pro rozšířené zabezpečení virtuálních [počítačů s Windows](windows/disk-encryption-overview.md) a virtuálních počítačů se systémem [Linux](linux/disk-encryption-overview.md) můžou být virtuální disky v Azure šifrované. Virtuální disky na virtuálních počítačích s Windows jsou v klidovém stavu šifrované pomocí nástroje BitLocker. Virtuální disky na virtuálních počítačích se systémem Linux jsou v klidovém stavu zašifrované pomocí dm-crypt. 

Za šifrování virtuálních disků v Azure se neúčtují žádné poplatky. Kryptografické klíče jsou uložené v Azure Key Vault pomocí ochrany softwaru nebo můžete klíče importovat nebo generovat v modulech hardwarového zabezpečení (HSM) certifikovaným pro standardy standardu FIPS 140-2 úrovně 2. Tyto kryptografické klíče slouží k šifrování a dešifrování virtuálních disků připojených k vašemu VIRTUÁLNÍmu počítači. Podržíte kontrolu nad těmito kryptografickými klíči a můžete auditovat jejich použití. Azure Active Directory instanční objekt poskytuje zabezpečený mechanismus pro vydávání těchto kryptografických klíčů, protože virtuální počítače jsou zapnuté a vypnuté.

## <a name="key-vault-and-ssh-keys"></a>Key Vault a klíče SSH

Tajné klíče a certifikáty je možné modelovat jako prostředky a poskytované [Key Vault](../key-vault/general/basic-concepts.md). Azure PowerShell můžete použít k vytvoření trezorů klíčů pro [virtuální počítače s Windows](windows/key-vault-setup.md) a Azure CLI pro [virtuální počítače](linux/key-vault-setup.md)se systémem Linux. Můžete také vytvořit klíče pro šifrování.

Zásady přístupu trezoru klíčů udělují samostatně oprávnění klíčům, tajným klíčům a certifikátům. Můžete tak například uživateli udělit přístup pouze ke klíčům, ale žádná oprávnění k tajným klíčům. Nicméně oprávnění pro přístup ke klíčům, tajným klíčům a certifikátům se nastavují na úrovni trezoru. [Zásady přístupu trezoru klíčů](../key-vault/general/security-overview.md) nepodporují oprávnění na úrovni objektů.

Když se připojíte k virtuálním počítačům, měli byste použít kryptografii s veřejným klíčem k zajištění bezpečnějšího způsobu, jak se k nim přihlašovat. Tento proces zahrnuje použití příkazu Secure Shell (SSH) k ověřování, a ne k uživatelskému jménu a heslu. Hesla jsou zranitelná proti útokům hrubou silou, zejména u virtuálních počítačů s přístupem k Internetu, jako jsou webové servery. Pomocí páru klíčů SSH (Secure Shell) můžete vytvořit [virtuální počítač Linux](linux/mac-create-ssh-keys.md) , který pro ověřování používá klíče SSH. tím se eliminuje nutnost přihlášení k heslům. Klíče SSH můžete použít také k připojení z [virtuálního počítače s Windows](linux/ssh-from-windows.md) k virtuálnímu počítači se systémem Linux.

## <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. 

Tento problém řeší funkce spravovaných identit prostředků Azure v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.  Váš kód, který běží na virtuálním počítači, může požádat o token ze dvou koncových bodů, které jsou přístupné jenom z virtuálního počítače. Podrobnější informace o této službě najdete na stránce Přehled [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) .   

## <a name="policies"></a>Zásady

[Zásady Azure](../governance/policy/overview.md) se dají použít k definování požadovaného chování pro [virtuální počítače s Windows](./windows/policy.md) a [virtuální počítače](./linux/policy.md)se systémem Linux. Pomocí zásad může organizace vyhovět různým konvencím a pravidlům v celém podniku. Vynucování požadovaného chování může přispět k zmírnění rizika při přispívání na úspěch organizace.

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Pomocí [řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md)můžete oddělit povinnosti v rámci svého týmu a udělit uživatelům jenom přístup k vašemu virtuálnímu počítači, který potřebují k provádění svých úloh. Místo udělení všech neomezených oprávnění na virtuálním počítači můžete použít jenom určité akce. Řízení přístupu pro virtuální počítač můžete nakonfigurovat v [Azure Portal](../role-based-access-control/role-assignments-portal.md)pomocí rozhraní příkazového [řádku Azure](/cli/azure/role)nebo[Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Další kroky
- Projděte si kroky pro monitorování zabezpečení virtuálních počítačů pomocí Azure Security Center pro [Linux](../security/fundamentals/overview.md) nebo [Windows](/previous-versions/azure/virtual-machines/tutorial-azure-security).