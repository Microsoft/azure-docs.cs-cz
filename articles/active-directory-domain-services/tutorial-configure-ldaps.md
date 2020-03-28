---
title: Kurz – Konfigurace protokolu LDAPS pro službu Azure Active Directory Domain Services | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak nakonfigurovat protokol LDAPS (Secure Lightweight Directory Access Protocol) pro spravovanou doménu služby Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 6db2c907abc495ca3c88e1e73e885043a8f19997
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481530"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Kurz: Konfigurace zabezpečeného protokolu LDAP pro spravovanou doménu služby Azure Active Directory Domain Services

Ke komunikaci se spravovanou doménou služby Azure Active Directory (Azure AD DS) se používá protokol LDAP (LDAP). Ve výchozím nastavení není provoz LDAP šifrován, což je problém zabezpečení pro mnoho prostředí. Pomocí služby Azure AD DS můžete nakonfigurovat spravovanou doménu tak, aby používala protokol LDAPS (Secure Lightweight Directory Access Protocol). Při použití zabezpečeného protokolu LDAP je provoz šifrován. Zabezpečené protokol LDAP je také známé jako LDAP přes ssl (SSL) / Transport Layer Security (TLS).

Tento kurz ukazuje, jak nakonfigurovat LDAPS pro spravovanou doménu Azure AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření digitálního certifikátu pro použití s Azure AD DS
> * Povolení zabezpečeného protokolu LDAP pro službu Azure AD DS
> * Konfigurace zabezpečeného protokolu LDAP pro použití přes veřejný internet
> * Vazba a testování zabezpečeného protokolu LDAP pro spravovanou doménu Azure AD DS

Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Nástroj *LDP.exe* nainstalovaný v počítači.
    * V případě potřeby [nainstalujte nástroje pro vzdálenou správu serveru (RSAT)][rsat] pro *služby Active Directory Domain Services a LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu nakonfigurujete zabezpečené LDAP pro spravovanou doménu Azure AD DS pomocí portálu Azure. Chcete-li začít, nejprve se přihlaste na [portál Azure](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Vytvoření certifikátu pro zabezpečené protokol LDAP

Chcete-li použít zabezpečené protokol LDAP, používá se k šifrování komunikace digitální certifikát. Tento digitální certifikát se použije pro vaši spravovanou doménu Azure AD DS a umožňuje nástrojům, jako je *LDP.exe,* používat zabezpečenou šifrovanou komunikaci při dotazování na data. Certifikát pro zabezpečený přístup LDAP ke spravované doméně lze vytvořit dvěma způsoby:

* Certifikát od certifikační autority (CA) nebo certifikační autority rozlehlé sítě.
    * Pokud vaše organizace získává certifikáty od veřejné certifikační autority, získejte od této veřejné certifikační autority zabezpečený certifikát LDAP. Pokud používáte certifikační autoritu rozlehlé sítě ve vaší organizaci, získejte zabezpečený certifikát LDAP od certifikační autority rozlehlé sítě.
    * Veřejná certifikační autorita funguje jenom v případě, že používáte vlastní název DNS se spravovanou doménou Azure AD DS. Pokud název domény DNS spravované domény končí na *.onmicrosoft.com*, nelze vytvořit digitální certifikát pro zabezpečení připojení k této výchozí doméně. Společnost Microsoft vlastní doménu *.onmicrosoft.com,* takže veřejná certifikační autorita nebude certifikát vydaná. V tomto scénáři vytvořte certifikát podepsaný svým držitelem a použijte jej ke konfiguraci zabezpečeného protokolu LDAP.
* Certifikát podepsaný svým držitelem, který si sami vytvoříte.
    * Tento přístup je vhodná pro účely testování a je to, co tento kurz ukazuje.

Certifikát, který požadujete nebo vytvoříte, musí splňovat následující požadavky. Spravovaná doména narazí na problémy, pokud povolíte zabezpečené protokol LDAP s neplatným certifikátem:

* **Důvěryhodný vystavitel** – Certifikát musí být vydán autoritou důvěryhodnou počítači, které se připojují ke spravované doméně pomocí zabezpečeného protokolu LDAP. Tímto úřadem může být veřejná certifikační autorita nebo certifikační autorita rozlehlé sítě, které tyto počítače důvěřují.
* **Životnost** - Certifikát musí být platný alespoň po dobu následujících 3-6 měsíců. Zabezpečený přístup LDAP ke spravované doméně je po vypršení platnosti certifikátu narušen.
* **Název subjektu** - Název subjektu na certifikátu musí být vaší spravovanou doménou. Pokud je například vaše doména pojmenována *aaddscontoso.com*, musí být název předmětu certifikátu **.aaddscontoso.com*.
    * Název DNS nebo alternativní název předmětu certifikátu musí být zástupný certifikát, aby bylo zajištěno, že zabezpečené protokol LDAP správně funguje se službou Azure AD Domain Services. Řadiče domény používají náhodné názvy a lze je odebrat nebo přidat, aby byla služba i nadále dostupná.
* **Použití klíče** – certifikát musí být nakonfigurován pro *digitální podpisy* a *zakódování klíčů*.
* **Účel certifikátu** – Certifikát musí být platný pro ověřování serveru TLS.

Existuje několik nástrojů, které jsou k dispozici pro vytvoření certifikátu podepsaného svým držitelem, jako jsou OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] rutina atd. V tomto kurzu vytvoříme certifikát podepsaný svým držitelem pro zabezpečený protokol LDAP pomocí rutiny [New-SelfSignedCertificate.][New-SelfSignedCertificate] Otevřete okno PowerShellu jako **správce** a spusťte následující příkazy. Nahraďte *proměnnou $dnsName* názvem DNS používaným vlastní spravovanou doménou, *například aaddscontoso.com*:

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Následující příklad výstupu ukazuje, že certifikát byl úspěšně vygenerován a je uložen v místním úložišti certifikátů (*LocalMachine\MY*):

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

Chcete-li použít zabezpečené protokol LDAP, je síťový provoz šifrován pomocí infrastruktury veřejných klíčů (PKI).

* Soukromý **private** klíč se použije pro spravovanou doménu Azure AD DS.
    * Tento soukromý klíč se používá k *dešifrování* zabezpečeného přenosu LDAP. Soukromý klíč by měl být použit pouze pro spravovanou doménu Služby Azure AD DS a není široce distribuován do klientských počítačů.
    * Certifikát, který obsahuje soukromý klíč, používá *. Formát* souboru PFX.
* Veřejný **public** klíč je použit pro klientské počítače.
    * Tento veřejný klíč se používá k *šifrování* zabezpečeného provozu LDAP. Veřejný klíč lze distribuovat do klientských počítačů.
    * Certifikáty bez soukromého klíče používají *. CER* formát souboru.

Tyto dva klíče, *soukromé* a *veřejné* klíče, ujistěte se, že pouze příslušné počítače mohou úspěšně komunikovat mezi sebou. Pokud používáte veřejnou certifikační autoritu nebo certifikační autoritu rozlehlé sítě, je vám vydán certifikát, který obsahuje soukromý klíč a dá se použít na spravovanou doménu Azure AD DS. Veřejný klíč by již měl být klientskými počítači znám a důvěryhodný. V tomto kurzu jste vytvořili certifikát podepsaný svým držitelem se soukromým klíčem, takže je třeba exportovat příslušné soukromé a veřejné součásti.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Export certifikátu pro Azure AD DS

Než budete moci použít digitální certifikát vytvořený v předchozím kroku se spravovanou doménou Služby Azure AD DS, exportujte certifikát do *. *Soubor certifikátu PFX, který obsahuje soukromý klíč.

1. Chcete-li otevřít dialogové okno *Spustit,* vyberte klávesy **Windows** a **R.**
1. Otevřete konzolu MMC (MMC) zadáním **konzoly MMC** v dialogovém okně *Spustit* a vyberte **možnost OK**.
1. Na výzvu **Řízení uživatelských účtů** klepnutím na tlačítko **Ano** spusťte konzolu MMC jako správce.
1. V nabídce **Soubor** klikněte na **Přidat nebo odebrat modul snap-in...**
1. V průvodci **modulu snap-in Certifikáty** zvolte **Účet počítače**a pak vyberte **Další**.
1. Na stránce **Vybrat počítač** zvolte **Místní počítač: (počítač, na který je tato konzola spuštěna)** a pak vyberte **Dokončit**.
1. V dialogovém okně **Přidat nebo odebrat moduly snap-in** přidejte do konzoly MMC klepnutím na **tlačítko OK.**
1. V okně Konzoly MMC rozbalte **položku Kořenová konzola**. Vyberte **certifikáty (místní počítač)** a rozbalte **osobní** uzel následovaný uzětem **Certifikáty.**

    ![Otevření úložiště osobních certifikátů v konzole MMC](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Zobrazí se certifikát podepsaný svým držitelem vytvořený v předchozím kroku, *například aaddscontoso.com*. Vyberte tento certifikát vpravo a pak zvolte **Všechny úkoly > export...**

    ![Export certifikátu v konzole MMC](./media/tutorial-configure-ldaps/export-cert.png)

1. V **Průvodci exportem certifikátu**vyberte **další**.
1. Soukromý klíč certifikátu musí být exportován. Pokud soukromý klíč není součástí exportovaného certifikátu, akce umožňující zabezpečenou protokol LDAP pro spravovanou doménu se nezdaří.

    Na stránce **Exportovat soukromý klíč** zvolte **Ano, exportujte soukromý klíč**a pak vyberte **Další**.
1. Spravované domény Azure AD DS podporují jenom *. *Formát souboru certifikátu PFX, který obsahuje soukromý klíč. Neexportujte certifikát jako *. *Formát souboru certifikátu CER bez soukromého klíče.

    Na stránce **Exportovat formát souboru** vyberte **Výměna osobních informací – #12 (PKCS). PFX)** jako formát souboru pro exportovaný certifikát. Pokud je to možné, zaškrtněte políčko *Zahrnout všechny certifikáty do certifikační cesty*:

    ![Zvolte možnost exportu certifikátu v PKCS 12 (. PFX) formát souboru](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Vzhledem k tomu, že tento certifikát se používá k dešifrování dat, měli byste pečlivě řídit přístup. Heslo lze použít k ochraně použití certifikátu. Bez správného hesla nelze certifikát použít pro službu.

    Na stránce **Zabezpečení** zvolte možnost **Heslo** k ochraně *. *Soubor certifikátu PFX. Zadejte a potvrďte heslo a pak vyberte **Další**. Toto heslo se používá v další části k povolení zabezpečené LDAP pro vaši spravovanou doménu Azure AD DS.
1. Na stránce **Soubor k exportu** zadejte název souboru a umístění, kam chcete certifikát exportovat, například *C:\Users\accountname\azure-ad-ds.pfx*. Poznamenejte si heslo a umístění *souboru . PFX,* protože tyto informace by byly vyžadovány v dalších krocích.
1. Na stránce revize vyberte **Dokončit,** chcete-li exportovat certifikát do *. *Soubor certifikátu PFX. Po úspěšném exportu certifikátu se zobrazí potvrzovací dialog.
1. Konzolu MMC ponechte otevřenou pro použití v následující části.

### <a name="export-a-certificate-for-client-computers"></a>Export certifikátu pro klientské počítače

Klientské počítače musí důvěřovat vystavitte zabezpečeného certifikátu LDAP, aby se mohl úspěšně připojit ke spravované doméně pomocí protokolu LDAPS. Klientské počítače potřebují certifikát k úspěšnému šifrování dat dešifrovaných službou Azure AD DS. Pokud používáte veřejnou certifikační autoritu, měl by počítač těmto vystavitelům certifikátů automaticky důvěřovat a mít odpovídající certifikát. V tomto kurzu použijete certifikát podepsaný svým držitelem a vygenerujete certifikát, který obsahuje soukromý klíč v předchozím kroku. Nyní exportujeme a nainstalujeme certifikát podepsaný svým držitelem do důvěryhodného úložiště certifikátů v klientském počítači:

1. Vraťte se do konzoly MMC pro *certifikáty (místní počítač) > úložiště osobních > certifikátů.* Zobrazí se certifikát podepsaný svým držitelem vytvořený v předchozím kroku, *například aaddscontoso.com*. Vyberte tento certifikát vpravo a pak zvolte **Všechny úkoly > export...**
1. V **Průvodci exportem certifikátu**vyberte **další**.
1. Vzhledem k tomu, že nepotřebujete soukromý klíč pro klienty, na stránce **Exportovat soukromý klíč** zvolte **Ne, neexportujte soukromý klíč**a pak vyberte **Další**.
1. Na stránce **Exportovat formát souboru** vyberte **Base-64 kódovaný X.509 (. CER)** jako formát souboru pro exportovaný certifikát:

    ![Zvolte možnost exportu certifikátu v base-64 kódovanéx.509 (. CER) formát souboru](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na stránce **Soubor k exportu** zadejte název souboru a umístění, kam chcete certifikát exportovat, například *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Na stránce revize vyberte **Dokončit,** chcete-li exportovat certifikát do *. *soubor certifikátu CER. Po úspěšném exportu certifikátu se zobrazí potvrzovací dialog.

V *. *Soubor certifikátu CER lze nyní distribuovat do klientských počítačů, které potřebují důvěřovat zabezpečenému připojení LDAP ke spravované doméně Azure AD DS. Nainstalujeme certifikát do místního počítače.

1. Otevřete Průzkumníka souborů a vyhledejte umístění, kam jste uložili *. *například *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Vyberte vpravo *. *a pak zvolte **Instalovat certifikát**.
1. V **Průvodci importem certifikátu**zvolte uložení certifikátu v *místním počítači*a vyberte **možnost Další**:

    ![Zvolte možnost importu certifikátu do místního úložiště počítače.](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Po zobrazení výzvy zvolte **Ano,** chcete-li počítači povolit provádění změn.
1. Zvolte **automaticky vybrat úložiště certifikátů na základě typu certifikátu**a pak vyberte **Další**.
1. Na stránce recenze vyberte **Dokončit,** chcete-li importovat *. certifikát CER.* soubor Po úspěšném importu certifikátu se zobrazí potvrzovací dialog.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Povolení zabezpečeného protokolu LDAP pro službu Azure AD DS

S vytvořeným a exportovaným digitálním certifikátem, který obsahuje soukromý klíč, a klientským počítačem nastaveným na důvěryhodnost připojení teď povolte zabezpečené protokol LDAP ve spravované doméně Azure AD DS. Chcete-li povolit zabezpečené protokol LDAP ve spravované doméně Azure AD DS, proveďte následující kroky konfigurace:

1. Na [webu Azure Portal](https://portal.azure.com)zadejte *služby domény* do pole Hledat **prostředky.** Z výsledku hledání vyberte **služby Azure AD Domain Services.**

    ![Hledání a výběr spravované domény Azure AD DS na webu Azure Portal](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. Na levé straně okna Azure AD DS zvolte **Secure LDAP**.
1. Ve výchozím nastavení je zabezpečený přístup LDAP ke spravované doméně zakázán. Přepnout **zabezpečené protokol LDAP** na **povolení**.
1. Zabezpečený přístup LDAP ke spravované doméně přes Internet je ve výchozím nastavení zakázán. Pokud povolíte veřejný zabezpečený přístup LDAP, vaše doména je náchylná k útokům hrubou silou hesla přes internet. V dalším kroku je skupina zabezpečení sítě nakonfigurována tak, aby uzamykala přístup pouze k požadovaným rozsahům zdrojových adres IP.

    Přepnout **povolit zabezpečený přístup LDAP přes Internet** k **povolení**.

1. Vyberte ikonu složky vedle položky **. Soubor PFX se zabezpečeným certifikátem LDAP**. Přejděte na cestu *. PFX* a vyberte certifikát vytvořený v předchozím kroku, který obsahuje soukromý klíč.

    Jak je uvedeno v předchozí části o požadavcích na certifikát, nelze použít certifikát z veřejné certifikační autority s výchozí doménou *onmicrosoft.com.* Společnost Microsoft vlastní doménu *.onmicrosoft.com,* takže veřejná certifikační autorita nebude certifikát vydaná. Zkontrolujte, zda je certifikát v příslušném formátu. Pokud tomu tak není, platforma Azure generuje chyby ověření certifikátu, když povolíte zabezpečené LDAP.

1. Zadejte **heslo pro dešifrování . Soubor PFX** nastavený v předchozím kroku při exportu certifikátu do *. Soubor PFX.*
1. Chcete-li povolit zabezpečené protokol LDAP, vyberte **možnost Uložit.**

    ![Povolení zabezpečeného protokolu LDAP pro spravovanou doménu Azure AD DS na webu Azure Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Zobrazí se oznámení, že pro spravovanou doménu je konfigurováno zabezpečené protokol LDAP. Další nastavení spravované domény nelze změnit, dokud nebude tato operace dokončena.

Povolení zabezpečeného protokolu LDAP pro spravovanou doménu trvá několik minut. Pokud zabezpečený certifikát LDAP, který zadáte, neodpovídá požadovaným kritériím, akce umožňující zabezpečení protokolu LDAP pro spravovanou doménu se nezdaří. Některé běžné důvody selhání jsou v případě, že název domény je nesprávný, nebo certifikát brzy vyprší nebo již vypršela platnost certifikátu. Certifikát s platnými parametry můžete znovu vytvořit a pomocí tohoto aktualizovaného certifikátu povolit zabezpečené protokol LDAP.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Uzamknutí zabezpečeného přístupu LDAP přes internet

Když povolíte zabezpečený přístup LDAP přes internet do spravované domény Azure AD DS, vytvoří hrozbu zabezpečení. Spravovaná doména je dostupná z internetu na portu TCP 636. Doporučujeme omezit přístup ke spravované doméně na konkrétní známé IP adresy pro vaše prostředí. Pravidlo skupiny zabezpečení sítě Azure lze použít k omezení přístupu k zabezpečenému protokolu LDAP.

Vytvořme pravidlo umožňující příchozí zabezpečený přístup LDAP přes port TCP 636 ze zadané sady adres IP. Výchozí pravidlo *DenyAll* s nižší prioritou platí pro všechny ostatní příchozí provozy z Internetu, takže pouze zadané adresy mohou dosáhnout vaší spravované domény Azure AD DS pomocí zabezpečeného protokolu LDAP.

1. Na portálu Azure vyberte *skupiny prostředků* na levé straně navigace.
1. Zvolte skupinu prostředků, například *myResourceGroup*, a vyberte skupinu zabezpečení sítě, *například aaads-nsg*.
1. Zobrazí se seznam existujících příchozích a odchozích pravidel zabezpečení. Na levé straně oken skupiny zabezpečení sítě zvolte **Nastavení > Příchozí pravidla zabezpečení**.
1. Vyberte **Přidat**a vytvořte pravidlo pro povolení portu *TCP* *636*. Chcete-li zlepšit zabezpečení, zvolte zdroj jako *adresy IP* a zadejte vlastní platnou adresu IP nebo rozsah pro vaši organizaci.

    | Nastavení                           | Hodnota        |
    |-----------------------------------|--------------|
    | Zdroj                            | IP adresy |
    | Zdrojové IP adresy / rozsahy CIDR | Platná IP adresa nebo rozsah pro vaše prostředí |
    | Rozsahy zdrojových portů                | *            |
    | Cíl                       | Všechny          |
    | Rozsahy cílových portů           | 636          |
    | Protocol (Protokol)                          | TCP          |
    | Akce                            | Povolit        |
    | Priorita                          | 401          |
    | Name (Název)                              | AllowLDAPS   |

1. Až budete připraveni, vyberte **Přidat,** chcete-li pravidlo uložit a použít.

    ![Vytvoření pravidla skupiny zabezpečení sítě pro zabezpečení přístupu ldaps přes internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Konfigurace zóny DNS pro externí přístup

Pokud je zabezpečený přístup LDAP povolen přes internet, aktualizujte zónu DNS tak, aby klientské počítače mohly najít tuto spravovanou doménu. *Externí IP adresa Secure LDAP* je uvedená na kartě **Vlastnosti** pro spravovanou doménu Azure AD DS:

![Zobrazení zabezpečené externí IP adresy LDAP pro spravovanou doménu Azure AD DS na webu Azure Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Nakonfigurujte externího poskytovatele DNS tak, aby vytvořil záznam hostitele, například *ldaps*, který bude vyřešen na tuto externí adresu IP. Chcete-li nejprve testovat místně v počítači, můžete vytvořit položku v souboru hosts systému Windows. Chcete-li úspěšně upravit soubor hosts v místním počítači, otevřete *poznámkový blok* jako správce a potom otevřete soubor *C:\Windows\System32\drivers\etc*

Následující příklad položky DNS, buď s externím poskytovatelem DNS, nebo v místním souboru hosts, řeší provoz pro *ldaps.aaddscontoso.com* na externí IP adresu *40.121.19.239*:

```
40.121.19.239    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testování dotazů do spravované domény

Chcete-li se připojit a vytvořit vazbu na spravovanou doménu Azure AD DS a hledat přes LDAP, použijte nástroj *LDP.exe.* Tento nástroj je součástí balíčku Nástroje pro vzdálenou správu serveru (RSAT). Další informace naleznete v [tématu instalace nástrojů pro vzdálenou správu serveru][rsat].

1. Otevřete *program LDP.exe* a připojte se ke spravované doméně. Vyberte **Připojení**, pak zvolte **Připojit...**.
1. Zadejte zabezpečený název domény LDAP DNS spravované domény vytvořený v předchozím kroku, například *ldaps.aaddscontoso.com*. Chcete-li použít zabezpečené protokol LDAP, nastavte **port** na *636*a zaškrtněte políčko **SSL**.
1. Chcete-li se připojit ke spravované doméně, vyberte **možnost OK.**

Dále svázat s vaší spravované domény Azure AD DS. Uživatelé (a účty služeb) nelze provádět LDAP jednoduché vazby, pokud jste zakázali ntlm synchronizace hash hesla na vaší instanci Azure AD DS. Další informace o zakázání synchronizace hash hesla NTLM najdete [v tématu Zabezpečení spravované domény Služby Azure AD DS][secure-domain].

1. Vyberte možnost nabídky **Připojení** a pak zvolte **Bind...**.
1. Zadejte pověření uživatelského účtu patřícího do *skupiny AAD DC Administrators,* například *contosoadmin*. Zadejte heslo uživatelského účtu a potom zadejte svou doménu, *například aaddscontoso.com*.
1. V **části Vaza zvolte**možnost *Vazby s pověřeními*.
1. Chcete-li vytvořit vazbu na spravovanou doménu Azure AD DS, vyberte **možnost OK.**

Chcete-li zobrazit objekty uložené ve spravované doméně Azure AD DS:

1. Vyberte volbu **Zobrazit** nabídku a pak zvolte **Strom**.
1. Ponechte pole *BaseDN* prázdné a pak vyberte **OK**.
1. Zvolte kontejner, například *Uživatelé AADDC*, pak vyberte kontejner vpravo a zvolte **Hledat**.
1. Ponechte předvyplněná pole nastavená a vyberte **Spustit**. Výsledky dotazu jsou zobrazeny v pravém okně.

    ![Hledání objektů ve spravované doméně Azure AD DS pomocí služby LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Chcete-li se přímo dotazovat na konkrétní kontejner, můžete z nabídky **Zobrazit > strom** zadat **basedn,** například *OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* nebo *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM*. Další informace o formátování a vytváření dotazů naleznete v [tématu Základy dotazů LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste do souboru místních hostitelů počítače přidali položku DNS, abyste otestovali připojení k tomuto kurzu, odeberte tuto položku a přidejte formální záznam do zóny DNS. Chcete-li položku odebrat ze souboru místních hostitelů, proveďte následující kroky:

1. V místním počítači otevřete *poznámkový blok* jako správce.
1. Přejděte a otevřete soubor *C:\Windows\System32\drivers\etc*
1. Odstraňte řádek přidaného záznamu, například`40.121.19.239    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření digitálního certifikátu pro použití s Azure AD DS
> * Povolení zabezpečeného protokolu LDAP pro službu Azure AD DS
> * Konfigurace zabezpečeného protokolu LDAP pro použití přes veřejný internet
> * Vazba a testování zabezpečeného protokolu LDAP pro spravovanou doménu Azure AD DS

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
