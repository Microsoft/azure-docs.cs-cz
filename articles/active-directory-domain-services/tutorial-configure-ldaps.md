---
title: Kurz – konfigurace LDAPs pro Azure Active Directory Domain Services | Microsoft Docs
description: V tomto kurzu se naučíte nakonfigurovat zabezpečený protokol LDAPs (Lightweight Directory Access Protocol) pro Azure Active Directory Domain Services spravovanou doménu.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: justinha
ms.openlocfilehash: fec2695c9e196a652a4166161bf012b22b0d00e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579548"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Kurz: Konfigurace zabezpečeného protokolu LDAP pro Azure Active Directory Domain Services spravovanou doménu

Ke komunikaci se spravovanou doménou Azure Active Directory Domain Services (Azure služba AD DS) se používá protokol LDAP (Lightweight Directory Access Protocol). Ve výchozím nastavení nejsou přenosy LDAP šifrované, což je bezpečnostní zabezpečení pro mnoho prostředí.

V případě Azure služba AD DS můžete nakonfigurovat spravovanou doménu tak, aby používala zabezpečený protokol LDAPs (Lightweight Directory Access Protocol). Pokud používáte zabezpečený protokol LDAP, přenos se zašifruje. Protokol Secure LDAP se také označuje jako protokol LDAP over SSL (Secure Sockets Layer) (SSL)/TLS (Transport Layer Security).

V tomto kurzu se dozvíte, jak nakonfigurovat LDAPs pro spravovanou doménu Azure služba AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření digitálního certifikátu pro použití s Azure služba AD DS
> * Povolit zabezpečený protokol LDAP pro Azure služba AD DS
> * Konfigurace zabezpečeného protokolu LDAP pro použití přes veřejný Internet
> * Vytvoření vazby a testování zabezpečeného protokolu LDAP pro spravovanou doménu

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* V počítači je nainstalovaný nástroj *LDP.exe* .
    * V případě potřeby [nainstalujte nástroje pro vzdálenou správu serveru (RSAT)][rsat] pro *Active Directory Domain Services a protokol LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu nakonfigurujete zabezpečený protokol LDAP pro spravovanou doménu pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Vytvoření certifikátu pro zabezpečený protokol LDAP

Chcete-li použít zabezpečený protokol LDAP, je k šifrování komunikace použit digitální certifikát. Tento digitální certifikát se použije ve vaší spravované doméně a umožňuje nástrojům, jako je *LDP.exe* při dotazování na data použít zabezpečenou šifrovanou komunikaci. Existují dva způsoby, jak vytvořit certifikát pro zabezpečený přístup LDAP ke spravované doméně:

* Certifikát od veřejné certifikační autority (CA) nebo certifikační autority organizace.
    * Pokud vaše organizace získá certifikáty od veřejné certifikační autority, Získejte certifikát zabezpečeného protokolu LDAP od této veřejné certifikační autority. Pokud ve vaší organizaci používáte certifikační autoritu organizace, Získejte certifikát zabezpečeného protokolu LDAP od certifikační autority organizace.
    * Veřejná certifikační autorita funguje jenom v případě, že používáte vlastní název DNS se spravovanou doménou. Pokud končí název domény DNS vaší spravované domény v *. onmicrosoft.com*, nemůžete vytvořit digitální certifikát pro zabezpečení připojení s touto výchozí doménou. Společnost Microsoft vlastní doménu *. onmicrosoft.com* , takže Veřejná certifikační autorita nevydá certifikát. V tomto scénáři vytvořte certifikát podepsaný svým držitelem a použijte ho ke konfiguraci zabezpečeného protokolu LDAP.
* Certifikát podepsaný svým držitelem, který vytvoříte sami.
    * Tento přístup je dobrý pro účely testování a je to, co tento kurz ukazuje.

Certifikát, který požadujete nebo vytvoříte, musí splňovat následující požadavky. Pokud povolíte zabezpečený protokol LDAP s neplatným certifikátem, zjistí vaše spravovaná doména tyto problémy:

* **Důvěryhodný Vystavitel** – certifikát musí být vydán autoritou, která je důvěryhodná pro počítače připojující se ke spravované doméně pomocí protokolu Secure LDAP. Tímto orgánem může být veřejná certifikační autorita nebo certifikační autorita organizace, které tyto počítače důvěřují.
* **Doba života** – certifikát musí být platný minimálně v následujících 3-6 měsících. Po vypršení platnosti certifikátu dojde k přerušení přístupu k spravované doméně protokol Secure LDAP.
* **Název subjektu** – název subjektu v certifikátu musí být vaše spravovaná doména. Pokud je vaše doména například s názvem *aaddscontoso.com*, musí být název subjektu certifikátu **. aaddscontoso.com*.
    * Název DNS nebo alternativní název subjektu certifikátu musí být certifikátem se zástupným znakem, aby zabezpečený protokol LDAP správně fungoval s Azure AD Domain Services. Řadiče domény používají náhodné názvy a je možné je odebrat nebo přidat, abyste zajistili, že služba zůstane dostupná.
* **Použití klíče** – certifikát musí být nakonfigurovaný pro *digitální podpisy* a *šifrování klíče*.
* **Účel certifikátu** – certifikát musí být platný pro ověřování serveru TLS.

K dispozici je několik nástrojů pro vytvoření certifikátu podepsaného svým držitelem, jako je OpenSSL, nástroj pro vytváření, MakeCert, rutina [New-SelfSignedCertificate][New-SelfSignedCertificate] atd.

V tomto kurzu vytvoříme certifikát podepsaný svým držitelem pro zabezpečení LDAP pomocí rutiny [New-SelfSignedCertificate][New-SelfSignedCertificate] .

Otevřete okno PowerShellu jako **správce** a spusťte následující příkazy. Nahraďte *$DnsName* PROMĚNNOU názvem DNS použitým vaší vlastní spravovanou doménou, například *aaddscontoso.com*:

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Následující příklad výstupu ukazuje, že certifikát se úspěšně vygeneroval a je uložený v místním úložišti certifikátů (*úložišti LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Pochopení a export požadovaných certifikátů

Aby bylo možné používat zabezpečený protokol LDAP, je síťový provoz zašifrovaný pomocí infrastruktury veřejných klíčů (PKI).

* **Privátní** klíč se použije ve spravované doméně.
    * Tento privátní klíč se používá k *dešifrování* zabezpečeného provozu LDAP. Privátní klíč by měl být použit pouze pro spravovanou doménu a není široce distribuován do klientských počítačů.
    * Certifikát, který obsahuje privátní klíč, používá *.* Formát souboru PFX.
    * Při exportování certifikátu je nutné zadat šifrovací algoritmus *TripleDES-SHA1* . To platí jenom pro soubor. pfx a nemá vliv na algoritmus používaný samotným certifikátem. Všimněte si, že možnost *TripleDES-SHA1* je k dispozici pouze od verze Windows Server 2016.
* **Veřejný** klíč se použije pro klientské počítače.
    * Tento veřejný klíč slouží k *šifrování* zabezpečeného přenosu LDAP. Veřejný klíč lze distribuovat do klientských počítačů.
    * Certifikáty bez privátního klíče používají *.* Formát souboru CER.

Tyto dva klíče, *privátní* a *veřejné* klíče, zajistí, že mezi sebou můžou úspěšně komunikovat jenom příslušné počítače. Pokud používáte veřejnou certifikační autoritu nebo certifikační autoritu organizace, vydáváte certifikát, který obsahuje privátní klíč, a můžete ho použít pro spravovanou doménu. Veřejný klíč by již měl být známý a důvěryhodný pro klientské počítače.

V tomto kurzu jste vytvořili certifikát podepsaný svým držitelem s privátním klíčem, takže potřebujete exportovat příslušné privátní a veřejné součásti.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Export certifikátu pro Azure služba AD DS

Předtím, než budete moci použít digitální certifikát vytvořený v předchozím kroku se spravovanou doménou, exportujte certifikát do *.* Soubor certifikátu PFX, který obsahuje privátní klíč.

1. Chcete-li otevřít dialogové okno *Spustit* , vyberte klíče **Windows**  +  **R** .
1. Otevřete konzolu MMC (Microsoft Management Console) tak, že v dialogovém okně *Spustit* zadáte **MMC** a pak vyberete **OK**.
1. Na příkazovém řádku pro **řízení uživatelských účtů** vyberte **Ano** , aby se MMC spouštěla jako správce.
1. V nabídce **soubor** vyberte **Přidat nebo odebrat modul snap-in...**
1. V průvodci **modulem snap-in Certifikáty** zvolte položku **účet počítače** a pak klikněte na tlačítko **Další**.
1. Na stránce **Vybrat počítač** zvolte **místní počítač: (počítač, na kterém je spuštěna tato konzola)** a pak vyberte **Dokončit**.
1. V dialogovém okně **Přidat nebo odebrat moduly snap-in** vyberte **OK** a přidejte modul snap-in Certifikáty do konzoly MMC.
1. V okně MMC rozbalte **kořen konzoly**. Vyberte **certifikáty (místní počítač)**, potom rozbalte **osobní** uzel a potom uzel **certifikáty** .

    ![Otevřete úložiště osobních certifikátů v konzole Microsoft Management Console.](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Zobrazí se certifikát podepsaný svým držitelem, který jste vytvořili v předchozím kroku, například *aaddscontoso.com*. Vyberte tento certifikát pravým tlačítkem a pak vyberte **všechny úlohy > exportovat...**

    ![Exportovat certifikát v konzole Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. V **Průvodci exportem certifikátu** vyberte **Další**.
1. Privátní klíč certifikátu musí být exportován. Pokud privátní klíč není zahrnutý v exportovaném certifikátu, akce pro povolení zabezpečeného LDAP pro spravovanou doménu se nezdařila.

    Na stránce **exportovat soukromý klíč** vyberte možnost **Ano, exportovat privátní klíč** a potom vyberte možnost **Další**.
1. Spravované domény podporují jenom *.* Formát souboru certifikátu PFX, který obsahuje privátní klíč. Neexportujte certifikát jako *.* Formát souboru certifikátu CER bez privátního klíče.

    Na stránce **Formát souboru pro export** vyberte **Personal Information Exchange-PKCS #12 (. PFX)** jako formát souboru pro exportovaný certifikát. Zaškrtněte políčko *Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné*:

    ![Vyberte možnost exportu certifikátu v PKCS 12 (. PFX) – formát souboru](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Vzhledem k tomu, že tento certifikát slouží k dešifrování dat, byste měli pečlivě řídit přístup. K ochraně použití certifikátu lze použít heslo. Bez správného hesla se certifikát nedá použít na službu.

    Na stránce **zabezpečení** vyberte možnost **heslo** pro ochranu *.* Soubor certifikátu PFX. Šifrovací algoritmus musí být *TripleDES-SHA1*. Zadejte a potvrďte heslo a pak vyberte **Další**. Toto heslo se používá v další části k povolení zabezpečeného protokolu LDAP pro spravovanou doménu.

    Pokud exportujete pomocí [rutiny PowerShellu export-vybíráte](/powershell/module/pkiclient/export-pfxcertificate), je potřeba předat příznak *-CryptoAlgorithmOption* pomocí TripleDES_SHA1.

    ![Snímek obrazovky s šifrováním hesla](./media/tutorial-configure-ldaps/encrypt.png)

1. Na stránce **soubor k exportu** zadejte název souboru a umístění, kam chcete certifikát exportovat, například *C:\Users\accountname\azure-AD-DS.pfx*. Poznamenejte si heslo a umístění *. Soubor PFX* jako tyto informace by byl nutný v následujících krocích.
1. Na stránce Kontrola vyberte **Dokončit** a exportujte certifikát do *.* Soubor certifikátu PFX. Po úspěšném exportu certifikátu se zobrazí potvrzovací dialogové okno.
1. Konzolu MMC nechte otevřenou pro použití v následující části.

### <a name="export-a-certificate-for-client-computers"></a>Export certifikátu pro klientské počítače

Klientské počítače musí důvěřovat vystaviteli certifikátu zabezpečeného protokolu LDAP, aby se mohly úspěšně připojit ke spravované doméně pomocí LDAPs. Klientské počítače potřebují certifikát k úspěšnému šifrování dat, která se dešifrují v Azure služba AD DS. Pokud používáte veřejnou certifikační autoritu, počítač by měl automaticky důvěřovat těmto vydavatelům certifikátů a mít odpovídající certifikát.

V tomto kurzu použijete certifikát podepsaný svým držitelem a vygenerujete certifikát, který obsahuje privátní klíč v předchozím kroku. Teď exportujte certifikát podepsaný svým držitelem do úložiště důvěryhodných certifikátů v klientském počítači a pak ho do něj nainstalujte sami:

1. Vraťte se do konzoly MMC pro *certifikáty (místní počítač) > úložiště certifikátů osobních >* . Zobrazí se certifikát podepsaný svým držitelem, který jste vytvořili v předchozím kroku, například *aaddscontoso.com*. Vyberte tento certifikát pravým tlačítkem a pak vyberte **všechny úlohy > exportovat...**
1. V **Průvodci exportem certifikátu** vyberte **Další**.
1. Vzhledem k tomu, že nepotřebujete privátní klíč pro klienty, vyberte na stránce **exportovat soukromý klíč** možnost **Ne, neexportovat privátní klíč** a pak vyberte **Další**.
1. Na stránce **Formát souboru pro export** vyberte **X. 509 kódovaný na bázi Base-64 (. CER)** jako formát souboru pro exportovaný certifikát:

    ![Vyberte možnost exportu certifikátu v kódování X. 509 kódované na bázi Base-64 (. CER) – formát souboru](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na stránce **soubor k exportu** zadejte název souboru a umístění, kam chcete certifikát exportovat, například *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Na stránce Kontrola vyberte **Dokončit** a exportujte certifikát do *.* Soubor certifikátu CER. Po úspěšném exportu certifikátu se zobrazí potvrzovací dialogové okno.

Rozhraní *.* Soubor certifikátu CER se teď dá distribuovat do klientských počítačů, které potřebují důvěřovat zabezpečenému připojení LDAP ke spravované doméně. Pojďme nainstalovat certifikát do místního počítače.

1. Otevřete Průzkumníka souborů a přejděte do umístění, kam jste uložili soubor *.* Soubor certifikátu CER, například *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Pravým tlačítkem myši vyberte *.* Soubor certifikátu CER a pak zvolte **nainstalovat certifikát**.
1. V **Průvodci importem certifikátu** zvolte možnost Uložit certifikát do *místního počítače* a pak vyberte **Další**:

    ![Vyberte možnost importu certifikátu do úložiště místního počítače.](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Po zobrazení výzvy vyberte **Ano** , pokud chcete, aby počítač mohl provádět změny.
1. Zvolte, **že se má automaticky vybrat úložiště certifikátů na základě typu certifikátu**, a pak vyberte **Další**.
1. Na stránce Kontrola vyberte **Dokončit** pro import *. Certifikát CER* Po úspěšném naimportování certifikátu se zobrazí potvrzovací dialogové okno souboru.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Povolit zabezpečený protokol LDAP pro Azure služba AD DS

Pomocí digitálního certifikátu vytvořeného a exportovaného, který obsahuje privátní klíč, a klientského počítače, který je nastavený tak, aby připojení důvěřoval, teď povolte zabezpečený protokol LDAP ve spravované doméně. Chcete-li povolit zabezpečený protokol LDAP ve spravované doméně, proveďte následující kroky konfigurace:

1. V [Azure Portal](https://portal.azure.com)zadejte *Domain Services* do pole **Hledat prostředky** . Ve výsledcích hledání vyberte **Azure AD Domain Services** .
1. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. Na levé straně okna Azure služba AD DS vyberte **protokol Secure LDAP**.
1. Ve výchozím nastavení je zabezpečený přístup protokolu LDAP k vaší spravované doméně zakázán. Přepněte **protokol Secure LDAP** k **Povolení**.
1. Ve výchozím nastavení je protokol Secure LDAP přístup ke spravované doméně prostřednictvím Internetu zakázán. Když povolíte veřejný zabezpečený přístup k LDAP, vaše doména je náchylná k útokům přes Internet pomocí hesla hrubou silou. V dalším kroku je skupina zabezpečení sítě nakonfigurovaná tak, aby se zamkl přístup jenom k požadovaným rozsahům zdrojových IP adres.

    Přepněte **Povolit zabezpečený přístup protokolu LDAP přes Internet** , abyste mohli **Povolit**.

1. Vyberte ikonu složky vedle **. Soubor PFX s certifikátem zabezpečeného protokolu LDAP**. Přejděte k cestě k *. Soubor PFX* a pak vyberte certifikát vytvořený v předchozím kroku, který obsahuje privátní klíč.

    > [!IMPORTANT]
    > Jak je uvedeno v předchozí části týkající se požadavků na certifikáty, nemůžete použít certifikát z veřejné certifikační autority s výchozí doménou *. onmicrosoft.com* . Společnost Microsoft vlastní doménu *. onmicrosoft.com* , takže Veřejná certifikační autorita nevydá certifikát.
    >
    > Ujistěte se, že je váš certifikát ve správném formátu. Pokud není, platforma Azure vygeneruje chyby ověření certifikátu, když povolíte zabezpečený protokol LDAP.

1. Zadejte **heslo pro dešifrování. Soubor PFX** , který jste nastavili v předchozím kroku, když byl certifikát exportován do *. Soubor PFX* .
1. Vyberte **Save (Uložit** ) a povolte zabezpečený protokol LDAP.

    ![Povolit zabezpečený protokol LDAP pro spravovanou doménu v Azure Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Zobrazí se oznámení o tom, že pro spravovanou doménu je nakonfigurovaný zabezpečený protokol LDAP. Dokud nebude tato operace dokončena, nelze změnit další nastavení pro spravovanou doménu.

Povolení zabezpečeného protokolu LDAP pro spravovanou doménu trvá několik minut. Pokud se certifikát zabezpečeného protokolu LDAP neshoduje s požadovanými kritérii, akce pro povolení zabezpečeného protokolu LDAP pro spravovanou doménu se nezdařila.

Některé běžné důvody selhání jsou v případě, že je název domény nesprávný, šifrovací algoritmus pro daný certifikát není *TripleDES-SHA1*, nebo brzy vyprší platnost certifikátu nebo již vypršela jeho platnost. Certifikát můžete znovu vytvořit s platnými parametry a pak povolit zabezpečený protokol LDAP pomocí tohoto aktualizovaného certifikátu.

## <a name="change-an-expiring-certificate"></a>Změna certifikátu s vypršenou platností

1. Pomocí postupu pro [Vytvoření certifikátu protokolu Secure LDAP](#create-a-certificate-for-secure-ldap)vytvořte náhradní certifikát zabezpečeného protokolu LDAP.
1. Pokud chcete použít náhradní certifikát pro Azure služba AD DS, v levé nabídce pro Azure služba AD DS v Azure Portal vyberte **protokol Secure LDAP** a pak vyberte **změnit certifikát**.
1. Distribuujte certifikát pro všechny klienty, kteří se připojují pomocí zabezpečeného LDAP. 

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Uzamknout zabezpečený přístup LDAP přes Internet

Když povolíte zabezpečený přístup pomocí protokolu LDAP přes Internet do spravované domény, vytvoří se bezpečnostní hrozba. Spravovaná doména je dosažitelná z Internetu na portu TCP 636. Doporučuje se omezit přístup ke spravované doméně na konkrétní známé IP adresy vašeho prostředí. Pravidlo skupiny zabezpečení sítě Azure se dá použít k omezení přístupu k zabezpečenému LDAP.

Pojďme vytvořit pravidlo, které umožní příchozí zabezpečený přístup LDAP přes port TCP 636 ze zadané sady IP adres. Výchozí pravidlo *denyall* s nižší prioritou se vztahuje na všechny ostatní příchozí přenosy z Internetu, takže se vaše spravovaná doména může dostat jenom přes zabezpečený protokol LDAP.

1. V Azure Portal na levé straně navigace vyberte *skupiny prostředků* .
1. Zvolte skupinu prostředků, třeba *myResourceGroup*, a pak vyberte skupinu zabezpečení sítě, třeba *aaads-NSG*.
1. Zobrazí se seznam existujících příchozích a odchozích pravidel zabezpečení. Na levé straně okna skupiny zabezpečení sítě vyberte **nastavení > příchozí pravidla zabezpečení**.
1. Vyberte **Přidat** a pak vytvořit pravidlo, které povolí  port TCP *636*. Pro lepší zabezpečení zvolte zdroj jako *IP adresy* a pak zadejte vlastní platnou IP adresu nebo rozsah pro vaši organizaci.

    | Nastavení                           | Hodnota        |
    |-----------------------------------|--------------|
    | Zdroj                            | IP adresy |
    | Zdrojové IP adresy/rozsahy CIDR | Platná IP adresa nebo rozsah pro vaše prostředí |
    | Rozsahy zdrojových portů                | *            |
    | Cíl                       | Libovolný          |
    | Rozsahy cílových portů           | 636          |
    | Protokol                          | TCP          |
    | Akce                            | Povolit        |
    | Priorita                          | 401          |
    | Name                              | AllowLDAPS   |

1. Až budete připraveni, vyberte **Přidat** a uložte a použijte pravidlo.

    ![Vytvoření pravidla skupiny zabezpečení sítě pro zabezpečení přístupu LDAP přes Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Konfigurace zóny DNS pro externí přístup

Když je přístup přes Internet zabezpečený pomocí protokolu LDAP, aktualizujte zónu DNS tak, aby klientské počítače mohly najít tuto spravovanou doménu. *Protokol Secure LDAP externí IP adresu* najdete na kartě **vlastnosti** spravované domény:

![Zobrazení externí IP adresy zabezpečeného protokolu LDAP pro spravovanou doménu v Azure Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Nakonfigurujte externího poskytovatele DNS tak, aby vytvořil záznam hostitele, například *LDAPS*, který se bude překládat na tuto externí IP adresu. K místnímu testování na svém počítači můžete vytvořit položku v souboru hostitelů systému Windows. Pokud chcete úspěšně upravit soubor hostitelů na místním počítači, otevřete *Poznámkový blok* jako správce a pak otevřete soubor *C:\Windows\System32\drivers\etc\hosts* .

Následující příklad položky DNS, buď s vaším externím poskytovatelem DNS, nebo v místním souboru hostitelů, vyřeší přenos pro *LDAPS.aaddscontoso.com* na externí IP adresu *168.62.205.103*:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testovat dotazy do spravované domény

Pokud se chcete připojit ke spravované doméně a prohledat ji přes LDAP, použijte nástroj *LDP.exe* . Tento nástroj je součástí balíčku Nástroje pro vzdálenou správu serveru (RSAT). Další informace najdete v tématu [instalace nástroje pro vzdálenou správu serveru][rsat].

1. Otevřete *LDP.exe* a připojte se ke spravované doméně. Vyberte **připojení** a pak zvolte **připojit...**.
1. Zadejte název domény DNS zabezpečeného LDAP vaší spravované domény, který jste vytvořili v předchozím kroku, například *LDAPS.aaddscontoso.com*. Chcete-li použít zabezpečený protokol LDAP, nastavte **port** na *636* a zaškrtněte políčko pro **protokol SSL**.
1. Vyberte **OK** a připojte se ke spravované doméně.

V dalším kroku se připojte ke spravované doméně. Uživatelé (a účty služeb) nemůžou provádět jednoduché vazby LDAP, pokud jste v spravované doméně zakázali synchronizaci hodnot hash hesel protokolu NTLM. Další informace o zakázání synchronizace hodnot hash hesel protokolu NTLM najdete v tématu [zabezpečení spravované domény][secure-domain].

1. Vyberte možnost nabídky **připojení** a pak zvolte **BIND...**.
1. Zadejte přihlašovací údaje uživatelského účtu, který patří do spravované domény. Zadejte heslo uživatelského účtu a pak zadejte svoji doménu, například *aaddscontoso.com*.
1. Pro **typ vazby** vyberte možnost *BIND s přihlašovacími údaji*.
1. Vyberte **OK** , aby se navázala vaše spravovaná doména.

Zobrazení objektů uložených ve spravované doméně:

1. Vyberte možnost nabídky **Zobrazit** a pak zvolte možnost **strom**.
1. Pole *BaseDN* ponechte prázdné a pak vyberte **OK**.
1. Zvolte kontejner, třeba *uživatele AADDC*, a pak klikněte pravým tlačítkem myši na kontejner a vyberte **Hledat**.
1. Nechejte předem vyplněná pole nastavena a pak vyberte **Spustit**. Výsledky dotazu se zobrazí v okně na pravé straně, jak je znázorněno v následujícím příkladu výstupu:

    ![Vyhledejte objekty ve spravované doméně pomocí LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Chcete-li přímo zadat dotaz na konkrétní kontejner, můžete v nabídce **zobrazit > stromu** zadat **BaseDN** jako *ou = AADDC Users, DC = AADDSCONTOSO, DC = com* nebo *ou = AADDC Computers, DC = AADDSCONTOSO, DC = com*. Další informace o tom, jak formátovat a vytvářet dotazy, najdete v tématu [základy dotazů LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste přidali položku DNS do souboru místní hostitelé vašeho počítače pro otestování připojení k tomuto kurzu, odeberte tuto položku a přidejte formální záznam do zóny DNS. Chcete-li odebrat záznam z místního souboru hostitelů, proveďte následující kroky:

1. Na místním počítači otevřete *Poznámkový blok* jako správce.
1. Vyhledejte a otevřete soubor *C:\Windows\System32\drivers\etc\hosts*
1. Odstraňte řádek pro záznam, který jste přidali, například `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření digitálního certifikátu pro použití s Azure služba AD DS
> * Povolit zabezpečený protokol LDAP pro Azure služba AD DS
> * Konfigurace zabezpečeného protokolu LDAP pro použití přes veřejný Internet
> * Vytvoření vazby a testování zabezpečeného protokolu LDAP pro spravovanou doménu

> [!div class="nextstepaction"]
> [Konfigurace synchronizace hodnot hash hesel pro hybridní prostředí Azure AD](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate