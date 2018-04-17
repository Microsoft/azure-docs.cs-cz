Je důležité k lepšímu zabezpečení pro aplikace, které můžete spustit virtuální počítač (VM). Zabezpečení virtuálních počítačů může obsahovat jednu nebo více služeb Azure a funkce, které se týkají zabezpečený přístup k virtuální počítače a zabezpečeného úložiště vaše data. Tento článek obsahuje informace, které umožňuje zachovat zabezpečení virtuálního počítače a aplikace.

## <a name="antimalware"></a>Antimalware

Povahu moderní hrozeb pro cloudové prostředí je dynamický, zvýšení nároků pro udržování ochrany efektivní aby byly splněny požadavky na zabezpečení a dodržování předpisů. [Antimalware od Microsoftu pro Azure](../articles/security/azure-security-antimalware.md) je bezplatná ochrany v reálném čase funkci, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Výstrahy lze konfigurovat pro upozornění, když označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na vašem virtuálním počítači.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) pomáhá zabránit, zjistit a reagovat na hrozby pro virtuální počítače. Security Center poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které byste jinak nevšimli a spolupracuje s řadou řešení zabezpečení.

## <a name="encryption"></a>Šifrování

Pro vylepšený [virtuální počítač s Windows](../articles/virtual-machines/windows/encrypt-disks.md) a [virtuálního počítače s Linuxem](../articles/virtual-machines/linux/encrypt-disks.md) zabezpečení a dodržování předpisů, virtuálních disků v Azure mohou být šifrována. Virtuální disky na virtuální počítače Windows jsou zašifrovaná přinejmenším pomocí nástroje Bitlocker. Virtuální disky na virtuální počítače s Linuxem jsou zašifrovaná přinejmenším pomocí dm-crypt. 

Není nijak zpoplatněn pro šifrování virtuálních disků v Azure. Kryptografické klíče ukládají v Azure Key Vault pomocí ochrany proti softwaru, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 úroveň 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Uchování kontroly nad těchto kryptografické klíče a můžete auditovat jejich použití. Hlavní služby Azure Active Directory poskytuje zabezpečené mechanismus pro vydávání tyto kryptografické klíče jako virtuální počítače jsou zapnuté zapnout a vypnout.

## <a name="key-vault-and-ssh-keys"></a>Trezor klíčů a klíče SSH

Tajných klíčů a certifikátů můžete modelován jako prostředky a poskytované [Key Vault](../articles/key-vault/key-vault-whatis.md). Prostředí Azure PowerShell můžete použít k vytvoření trezorů klíčů pro [virtuálních počítačů Windows](../articles/virtual-machines/windows/key-vault-setup.md) a rozhraní příkazového řádku Azure pro [virtuální počítače s Linuxem](../articles/virtual-machines/linux/key-vault-setup.md). Můžete také vytvořit klíče pro šifrování.

Zásady přístupu k trezoru klíčů udělit oprávnění k klíčů, tajných klíčů a certifikátů samostatně. Můžete tak například uživateli udělit přístup pouze ke klíčům, ale žádná oprávnění k tajným klíčům. Nicméně oprávnění pro přístup ke klíčům, tajným klíčům a certifikátům se nastavují na úrovni trezoru. Jinými slovy [klíče trezoru zásady přístupu](../articles/key-vault/key-vault-secure-your-key-vault.md) nepodporuje oprávnění na úrovni objektů.

Jakmile se připojíte k virtuálním počítačům, měli byste použít šifrování veřejného klíče zajistit bezpečnější způsob, jak se přihlásit k nim. Tento proces zahrnuje veřejné a privátní výměny klíčů pomocí příkazu zabezpečené shell (SSH) k ověření sami spíše než uživatelské jméno a heslo. Hesla se stát terčem útoků, zvláště na straně Internetu virtuálních počítačů, jako jsou třeba webové servery hrubou silou. Pomocí pár klíčů zabezpečené shell (SSH) můžete vytvářet [virtuálního počítače s Linuxem](../articles/virtual-machines/linux/mac-create-ssh-keys.md) používající klíče SSH k ověření, což eliminuje potřebu hesla k přihlášení. Můžete také použít klíče SSH připojit ze [virtuální počítač s Windows](../articles/virtual-machines/linux/ssh-from-windows.md) do virtuálního počítače s Linuxem.

## <a name="policies"></a>Zásady

[Azure zásady](../articles/azure-policy/azure-policy-introduction.md) lze použít k definování požadované chování pro vaši organizaci [virtuálních počítačů Windows](../articles/virtual-machines/windows/policy.md) a [virtuální počítače s Linuxem](../articles/virtual-machines/linux/policy.md). Pomocí zásad můžete vynutit organizaci různé konvence a pravidla v rámci podniku. Vynucení požadované chování může pomoci zmírnit rizika při přispívání do úspěch organizace.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pomocí [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md), můžete v rámci týmu oddělit povinností a poskytnout pouze takovou úroveň přístupu pro uživatele na vašem virtuálním počítači, které potřebují k provádění svých úloh. Namísto udělení každý uživatel neomezený oprávnění ve virtuálním počítači, můžete povolit jenom určité akce. Řízení přístupu můžete nakonfigurovat pro virtuální počítač v [portál Azure](../articles/role-based-access-control/role-assignments-portal.md)pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/role), nebo[prostředí Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Další postup
- Provede kroky pro sledování zabezpečení virtuálního počítače pomocí Azure Security Center pro [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).