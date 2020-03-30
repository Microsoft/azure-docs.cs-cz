---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14a6703b3e256d33ab3b18e1821587cc3eb293db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79382227"
---
Je důležité, aby váš virtuální počítač (VM) zabezpečené pro aplikace, které spustíte. Zabezpečení virtuálních počítačů může zahrnovat jednu nebo více služeb Azure a funkcí, které zahrnují zabezpečený přístup k vašim virtuálním počítačům a zabezpečené úložiště vašich dat. Tento článek obsahuje informace, které vám umožní zabezpečit virtuální počítač a aplikace.

## <a name="antimalware"></a>Antimalware

Moderní prostředí hrozeb pro cloudová prostředí je dynamické, což zvyšuje tlak na zachování účinné ochrany, aby byly splněny požadavky na dodržování předpisů a zabezpečení. [Microsoft Antimalware for Azure](../articles/security/fundamentals/antimalware.md) je bezplatná funkce ochrany v reálném čase, která pomáhá identifikovat a odstranit viry, spyware a další škodlivý software. Výstrahy lze nakonfigurovat tak, aby vás upozornily, když se známý škodlivý nebo nežádoucí software pokusí nainstalovat sám nebo spustit na vašem virtuálním počítači. Není podporována na virtuálních počítačích se systémem Linux nebo Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) vám pomůže předcházet, zjišťovat a reagovat na hrozby pro vaše virtuální počítače. Security Center poskytuje integrované monitorování zabezpečení a správu zásad v rámci vašich předplatných Azure, pomáhá odhalovat hrozby, které by jinak mohly zůstat bez povšimnutí, a spolupracuje s širokým ekosystémem řešení zabezpečení.

Přístup centra zabezpečení za časového nastavení lze použít v rámci nasazení virtuálních počítačích, aby se uzamkl příchozí provoz na virtuálních počítačích Azure, čímž se snižuje vystavení útokům a zároveň poskytuje snadný přístup k virtuálním počítačům v případě potřeby. Pokud je povoleno just-in-time a uživatel požaduje přístup k virtuálnímu virtuálnímu serveru, Security Center zkontroluje, jaká oprávnění má uživatel pro virtuální ho. Pokud mají správná oprávnění, je požadavek schválen a Centrum zabezpečení automaticky nakonfiguruje skupiny zabezpečení sítě (NSG), aby po omezenou dobu povolovaly příchozí přenosy na vybrané porty. Po uplynutí doby centrum zabezpečení obnoví skupiny zabezpečení do svých předchozích stavů. 

## <a name="encryption"></a>Šifrování

Pro spravované disky jsou nabízeny dvě metody šifrování. Šifrování na úrovni operačního systému, což je Šifrování disku Azure, a šifrování na úrovni platformy, což je šifrování na straně serveru.

### <a name="server-side-encryption"></a>Šifrování na straně serveru

Disky spravované Azure automaticky šifrují vaše data ve výchozím nastavení, když je uchovávají do cloudu. Šifrování na straně serveru chrání vaše data a pomáhá vám plnit závazky organizace v oblasti zabezpečení a dodržování předpisů. Data na spravovaných discích Azure se šifrují transparentně pomocí 256bitového [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejsilnějších blokových šifer, která je k dispozici, a je kompatibilní s FIPS 140-2.

Šifrování nemá vliv na výkon spravovaných disků. Šifrování neplatí žádné další náklady.

Pro šifrování spravovaného disku se můžete spolehnout na klíče spravované platformou nebo můžete spravovat šifrování pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem,* který se použije pro šifrování a dešifrování všech dat na spravovaných discích. 

Další informace o šifrování na straně serveru naleznete v článcích pro [Windows](../articles/virtual-machines/windows/disk-encryption.md) nebo [Linux](../articles/virtual-machines/linux/disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Pro vylepšené zabezpečení a dodržování předpisů [virtuálních](../articles/virtual-machines/windows/encrypt-disks.md) počítačů s Windows a [Linuxem](../articles/virtual-machines/linux/disk-encryption-overview.md) můžou být virtuální disky v Azure šifrované. Virtuální disky na virtuálních počítačích se systémem Windows jsou šifrovány v klidovém stavu pomocí nástroje BitLocker. Virtuální disky na virtuálních počítačích s Linuxem jsou šifrované v klidovém stavu pomocí dm-crypt. 

Šifrování virtuálních disků v Azure se neúčtuje. Kryptografické klíče jsou uloženy v trezoru klíčů Azure pomocí softwarové ochrany nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2. Tyto kryptografické klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Zachováte kontrolu nad těmito kryptografickými klíči a můžete auditovat jejich použití. Objekt zabezpečení služby Azure Active Directory poskytuje zabezpečený mechanismus pro vydávání těchto kryptografických klíčů, protože virtuální počítače jsou zapnuté a vypínaté.

## <a name="key-vault-and-ssh-keys"></a>Trezor klíčů a klíče SSH

Tajné klíče a certifikáty mohou být modelovány jako prostředky a poskytovány [trezorem klíčů](../articles/key-vault/key-vault-whatis.md). Azure PowerShell můžete použít k vytvoření trezorů klíčů pro [virtuální počítače s Windows](../articles/virtual-machines/windows/key-vault-setup.md) a Azure CLI pro virtuální počítače s [Linuxem](../articles/virtual-machines/linux/key-vault-setup.md). Můžete také vytvořit klíče pro šifrování.

Zásady přístupu trezoru klíčů udělují oprávnění klíčům, tajným klíčům a certifikátům samostatně. Můžete tak například uživateli udělit přístup pouze ke klíčům, ale žádná oprávnění k tajným klíčům. Nicméně oprávnění pro přístup ke klíčům, tajným klíčům a certifikátům se nastavují na úrovni trezoru. Jinými slovy [zásady přístupu k trezoru klíčů](../articles/key-vault/key-vault-secure-your-key-vault.md) nepodporuje oprávnění na úrovni objektu.

Když se připojíte k virtuálním kvosám, měli byste použít kryptografii s veřejným klíčem, abyste poskytli bezpečnější způsob přihlášení k nim. Tento proces zahrnuje výměnu veřejných a soukromých klíčů pomocí příkazu zabezpečeného prostředí (SSH) k ověření sebe sama, nikoli uživatelského jména a hesla. Hesla jsou zranitelná vůči útokům hrubou silou, zejména na internetových virtuálních počítačích, jako jsou webové servery. Se zabezpečeným párem klíčů prostředí (SSH) můžete vytvořit [virtuální počítač s Linuxem,](../articles/virtual-machines/linux/mac-create-ssh-keys.md) který používá klíče SSH pro ověřování, což eliminuje potřebu hesla pro přihlášení. Klíče SSH můžete také použít k připojení z [virtuálního počítače s Windows](../articles/virtual-machines/linux/ssh-from-windows.md) k virtuálnímu počítači s Linuxem.

## <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. 

Tento problém řeší funkce spravovaných identit prostředků Azure v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.  Váš kód, který běží na virtuálním počítači můžete požádat o token ze dvou koncových bodů, které jsou přístupné pouze z v rámci virtuálního počítače. Podrobnější informace o této službě najdete na stránce [přehledu spravovaných identit pro prostředky Azure.](../articles/active-directory/managed-identities-azure-resources/overview.md)   

## <a name="policies"></a>Zásady

[Zásady Azure](../articles/azure-policy/azure-policy-introduction.md) se můžou použít k definování požadovaného chování pro [virtuální počítače](../articles/virtual-machines/windows/policy.md) s Windows a virtuální počítače [s Linuxem](../articles/virtual-machines/linux/policy.md)ve vaší organizaci . Pomocí zásad může organizace vynucovat různé konvence a pravidla v celém podniku. Vynucení požadovaného chování může pomoci zmírnit riziko a zároveň přispět k úspěchu organizace.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pomocí [řízení přístupu na základě rolí (RBAC)](../articles/role-based-access-control/overview.md)můžete oddělit povinnosti v rámci týmu a udělit pouze množství přístupu uživatelům na vašem virtuálním počítači, které potřebují k provádění svých úloh. Místo toho, aby všichni neomezená oprávnění na virtuálním počítači, můžete povolit pouze určité akce. Řízení přístupu pro virtuální počítač můžete nakonfigurovat na [webu Azure Portal](../articles/role-based-access-control/role-assignments-portal.md), pomocí rozhraní [příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/role)nebo Azure[PowerShellu](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Další kroky
- Projděte si kroky ke sledování zabezpečení virtuálních strojů pomocí Azure Security Center pro [Linux](../articles/security/fundamentals/overview.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).
