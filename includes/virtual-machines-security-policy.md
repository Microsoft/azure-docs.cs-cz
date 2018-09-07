Je důležité k zabezpečení vašich virtuálních počítačů (VM) pro aplikace, které spustíte. Zabezpečení vašich virtuálních počítačů může obsahovat jednu nebo více služeb Azure a funkce, které se týkají zabezpečený přístup k virtuálním počítačům a zabezpečené ukládání vašich dat. Tento článek obsahuje informace, které umožňuje zabezpečit svůj virtuální počítač a aplikací.

## <a name="antimalware"></a>Antimalware

Na šířku moderními hrozbami pro prostředí cloud je dynamická, zvyšuje tlak na udržovat účinnou ochranu, aby se splnily požadavky na zabezpečení a dodržování předpisů. [Microsoft Antimalware pro Azure](../articles/security/azure-security-antimalware.md) je bezplatná ochrana v reálném čase, který pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software. Výstrahy lze nastavit upozornění při označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit ve virtuálním počítači.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) pomáhá zabránit, detekci a reakce na hrozby do virtuálních počítačů. Security Center poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které jinak nevšimli a spolupracuje s řadou řešení zabezpečení.

## <a name="encryption"></a>Šifrování

Pro rozšířené [virtuálního počítače Windows](../articles/virtual-machines/windows/encrypt-disks.md) a [virtuálního počítače s Linuxem](../articles/virtual-machines/linux/encrypt-disks.md) zabezpečení a dodržování předpisů, virtuálních disků v Azure mohou být zašifrovaná. Virtuální disky na virtuálních počítačích s Windows se šifrují při nečinnosti pomocí Bitlockeru. Virtuální disky na virtuální počítače s Linuxem se šifrují při nečinnosti pomocí dm-crypt. 

Neplatí žádné poplatky pro šifrování virtuálních disků v Azure. Kryptografické klíče jsou uložené ve službě Azure Key Vault software ochrany, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. Uchování kontroly nad těmito kryptografické klíče a můžete kontrolovat jejich použití. Instančního objektu služby Azure Active Directory poskytuje zabezpečené mechanismus pro vydávání těchto kryptografických klíčů, jako jsou virtuální počítače využívající zapnout a vypnout.

## <a name="key-vault-and-ssh-keys"></a>Trezor klíčů a klíče SSH

Tajné klíče a certifikáty můžete nemodelují jako prostředky a poskytuje [služby Key Vault](../articles/key-vault/key-vault-whatis.md). Prostředí Azure PowerShell můžete použít k vytvoření trezorů klíčů pro [virtuální počítače s Windows](../articles/virtual-machines/windows/key-vault-setup.md) a rozhraní příkazového řádku Azure pro [virtuální počítače s Linuxem](../articles/virtual-machines/linux/key-vault-setup.md). Můžete také vytvořit klíče pro šifrování.

Zásady přístupu trezoru klíčů udělují odděleně oprávnění pro klíče, tajné kódy a certifikáty. Můžete tak například uživateli udělit přístup pouze ke klíčům, ale žádná oprávnění k tajným klíčům. Nicméně oprávnění pro přístup ke klíčům, tajným klíčům a certifikátům se nastavují na úrovni trezoru. Jinými slovy [zásady přístupu trezoru klíčů](../articles/key-vault/key-vault-secure-your-key-vault.md) nepodporuje oprávnění na úrovni objektu.

Při připojení k virtuálním počítačům používejte kryptografie využívající veřejný klíč k poskytování bezpečnější způsob přihlašování k nim. Tento proces zahrnuje veřejných a privátních výměny klíčů pomocí příkazu (SSH secure shell) k ověření sebe spíše než uživatelské jméno a heslo. Hesla se stát terčem útoku hrubou silou útoky, zejména u virtuálních počítačů směřujících k Internetu, jako jsou třeba webové servery. Pomocí páru klíčů (SSH secure shell) můžete vytvořit [virtuálního počítače s Linuxem](../articles/virtual-machines/linux/mac-create-ssh-keys.md) , která používá klíče SSH pro ověřování, takže odpadá potřeba používat k přihlášení hesla. Můžete také použít klíče SSH pro připojení z [virtuálního počítače Windows](../articles/virtual-machines/linux/ssh-from-windows.md) do virtuálního počítače s Linuxem.

## <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. 

Tento problém řeší spravované identity pro funkce Azure prostředky v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.  Váš kód, který běží na virtuálním počítači můžete požádat o token dva koncové body, které jsou dostupné pouze z v rámci virtuálního počítače. Podrobné informace o této službě, přečtěte si [spravovaných identit pro prostředky Azure](../articles/active-directory/managed-identities-azure-resources/overview.md) stránka s přehledem.   

## <a name="policies"></a>Zásady

[Zásady Azure](../articles/azure-policy/azure-policy-introduction.md) slouží k definování požadované chování pro vaši organizaci [virtuální počítače s Windows](../articles/virtual-machines/windows/policy.md) a [virtuální počítače s Linuxem](../articles/virtual-machines/linux/policy.md). Pomocí zásad můžete vynutit organizace různých konvencí a pravidla v rámci podniku. Vynucení požadované chování může pomoci zmírnit rizika při podílení se na úspěch organizace.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pomocí [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md), můžete v rámci týmu oddělit a udělit uživatelům jenom takový přístup na virtuálním počítači, které potřebují ke své práci. Ne všichni poskytuje neomezená oprávnění na virtuálním počítači, můžete povolit pouze určité akce. Řízení přístupu můžete nakonfigurovat pro virtuální počítač v [webu Azure portal](../articles/role-based-access-control/role-assignments-portal.md), použije [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/role), nebo[prostředí Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Další postup
- Provede kroky k monitorování zabezpečení virtuálních počítačů pomocí Azure Security Center pro [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).