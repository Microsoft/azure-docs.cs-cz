---
title: Nainstalujte agenty Connect Health v Azure Active Directory
description: Tento článek Azure AD Connect Health popisuje instalaci agenta pro Active Directory Federation Services (AD FS) (AD FS) a pro synchronizaci.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e644b7937f6ccb23b4833405b8f4ed3119879a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362280"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalace agenta Azure AD Connect Health

V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat agenty služby Azure Active Directory (Azure AD) Connect Health. Chcete-li stáhnout agenty, [postupujte podle těchto pokynů](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Požadavky

V následující tabulce jsou uvedeny požadavky na použití Azure AD Connect Health.

| Požadavek | Popis |
| --- | --- |
| K dispozici je Azure AD Premium (P1 nebo P2) předplatném.  |Azure AD Connect Health je funkce Azure AD Premium (P1 nebo P2). Další informace najdete v tématu [Registrace k Azure AD Premium](../fundamentals/active-directory-get-started-premium.md). <br /><br />Pokud chcete začít bezplatnou 30denní zkušební verzi, přečtěte si téma [spuštění zkušební verze](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Jste globální správce ve službě Azure AD. |Ve výchozím nastavení můžou agenty stavu instalovat a konfigurovat jenom globální správci, přistupovat k portálu a provádět všechny operace v rámci Azure AD Connect Health. Další informace najdete v článku o [správě adresáře Azure AD](../fundamentals/active-directory-whatis.md). <br /><br /> Pomocí řízení přístupu na základě role v Azure (Azure RBAC) můžete ostatním uživatelům ve vaší organizaci dovolit přístup k Azure AD Connect Health. Další informace najdete v tématu [Azure RBAC pro Azure AD Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Důležité**: k instalaci agentů použijte pracovní nebo školní účet. Nemůžete použít účet Microsoft. Další informace najdete v tématu [Registrace do Azure jako organizace](../fundamentals/sign-up-organization.md). |
| Agent Azure AD Connect Health se instaluje na každý cílový server. | Agenti stavu musí být nainstalováni a nakonfigurováni na cílových serverech, aby mohli přijímat data a poskytovat možnosti monitorování a analýzy. <br /><br />Pokud například chcete získat data z infrastruktury Active Directory Federation Services (AD FS) (AD FS), musíte agenta nainstalovat na server AD FS a na server služby Proxy webových aplikací. Podobně pokud chcete získat data z místní infrastruktury Azure AD Domain Services (Azure služba AD DS), musíte agenta nainstalovat na řadiče domény.  |
| Koncové body služby Azure mají odchozí připojení. | Agent během instalace a za běhu vyžaduje připojení ke koncovým bodům služby Azure AD Connect Health. Pokud brány firewall blokují odchozí připojení, přidejte [koncové body odchozího připojení](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) do seznamu povolených. |
|Odchozí připojení vychází z IP adres. | Informace o filtrování brány firewall na základě IP adres najdete v tématu [rozsahy IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| Kontrola TLS pro odchozí přenosy je filtrovaná nebo zakázaná. | Krok registrace agenta nebo operace nahrávání dat můžou selhat, pokud dojde k prověřování nebo ukončení protokolu TLS pro odchozí přenosy v síťové vrstvě. Další informace najdete v tématu [Nastavení kontroly TLS](/previous-versions/tn-archive/ee796230(v=technet.10)). |
| Porty brány firewall na serveru spouštějí agenta. |Agent vyžaduje, aby následující porty brány firewall byly otevřené, aby mohly komunikovat s koncovými body služby Azure AD Connect Health: <br /><li>Port 443 protokolu TCP</li><li>Port 5671 protokolu TCP</li> <br />Nejnovější verze agenta nevyžaduje port 5671. Upgradujte na nejnovější verzi, aby se vyžadoval pouze port 443. Další informace najdete v tématu o [portech a protokolech potřebných pro hybridní identitu](./reference-connect-ports.md). |
| Pokud je zapnuté rozšířené zabezpečení aplikace Internet Explorer, povolte zadané weby.  |Pokud je zapnuté rozšířené zabezpečení aplikace Internet Explorer, povolte na serveru, kam instalujete agenta, následující weby:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.NET</li><li>Federační server vaší organizace, který je důvěryhodný pro Azure AD (například https: \/ /STS.contoso.com)</li> <br />Další informace najdete v tématu [Konfigurace aplikace Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Pokud máte v síti proxy server, podívejte se na poznámku, která se zobrazí na konci této tabulky.|
| Je nainstalováno prostředí PowerShell verze 4,0 nebo novější. | Windows Server 2012 obsahuje PowerShell verze 3,0. Tato *verze není pro agenta dostačující.*</br></br> Windows Server 2012 R2 a novější zahrnuje dostatečně nejnovější verzi PowerShellu.|
|Standard FIPS (Federal Information Processing Standard) je zakázán.|Agenti Azure AD Connect Health nepodporují standard FIPS.|

> [!IMPORTANT]
> Jádro Windows serveru nepodporuje instalaci agenta Azure AD Connect Health.


> [!NOTE]
> Pokud máte vysoce uzamčené a omezené prostředí, budete muset přidat další adresy URL, než jsou seznamy v tabulce pro rozšířené zabezpečení aplikace Internet Explorer. Přidejte také adresy URL, které jsou uvedeny v tabulce v následující části.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Odchozí připojení ke koncovým bodům služby Azure

Během instalace a modulu runtime potřebuje agent připojení k Azure AD Connect Health koncovým bodům služby. Pokud brány firewall blokují odchozí připojení, ujistěte se, že adresy URL v následující tabulce nejsou ve výchozím nastavení blokované. 

Neblokujte monitorování zabezpečení ani kontrolu těchto adres URL. Místo toho je povolte, protože byste povolili jiný internetový provoz. 

Tyto adresy URL umožňují komunikaci s Azure AD Connect Healthmi koncovými body služby. Později v tomto článku se dozvíte, jak [Ověřit odchozí připojení](#test-connectivity-to-azure-ad-connect-health-service) pomocí `Test-AzureADConnectHealthConnectivity` .

| Prostředí domény | Požadované koncové body služby Azure |
| --- | --- |
| Obecné veřejné | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-port: 5671 (tento koncový bod není v nejnovější verzi agenta vyžadován.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (tento koncový bod se používá jenom pro účely zjišťování během registrace).</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure (Německo) | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (tento koncový bod se používá jenom pro účely zjišťování během registrace).</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (tento koncový bod se používá jenom pro účely zjišťování během registrace).</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>Instalace agenta

Stažení a instalace agenta Azure AD Connect Health: 

* Ujistěte se, že splňujete [požadavky](how-to-connect-health-agent-install.md#requirements) na Azure AD Connect Health.
* Začínáme používat Azure AD Connect Health AD FS:
    * [Stáhněte agenta Azure AD Connect Health pro AD FS](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Projděte si [pokyny k instalaci](#install-the-agent-for-ad-fs).
* Začínáme používat Azure AD Connect Health k synchronizaci:
    * [Stažení a instalace nejnovější verze služby Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771) Agent stavu pro synchronizaci se nainstaluje jako součást instalace Azure AD Connect (verze 1.0.9125.0 nebo novější).
* Začínáme používat Azure AD Connect Health pro Azure služba AD DS:
    * [Stáhněte agenta Azure AD Connect Health pro Azure služba AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Projděte si [pokyny k instalaci](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Instalace agenta pro AD FS

> [!NOTE]
> Váš AD FS Server by měl být jiný než váš synchronizační Server. Neinstalujte do synchronizačního serveru agenta AD FS.
>

Než agenta nainstalujete, ujistěte se, že je název hostitele serveru AD FS jedinečný a ve službě AD FS neexistuje.
Chcete-li spustit instalaci agenta, poklikejte na stažený soubor *. exe* . V prvním okně vyberte **instalovat**.

![Snímek obrazovky zobrazující okno instalace agenta Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install1.png)

Po dokončení instalace vyberte **Konfigurovat nyní**.

![Snímek obrazovky s potvrzovací zprávou pro instalaci agenta Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install2.png)

Otevře se okno PowerShellu, ve kterém se spustí proces registrace agenta. Až se zobrazí výzva, přihlaste se pomocí účtu Azure AD, který má oprávnění k registraci agenta. Ve výchozím nastavení má účet globálního správce oprávnění.

![Snímek obrazovky se zobrazeným přihlašovacím oknem pro Azure AD Connect Health AD FS](./media/how-to-connect-health-agent-install/install3.png)

Po přihlášení pokračuje prostředí PowerShell. Po dokončení můžete PowerShell zavřít. Konfigurace je dokončena.

V tuto chvíli by se měly služby agenta spouštět automaticky, aby mohl agent bezpečně odeslat požadovaná data do cloudové služby.

Pokud jste nesplnili všechny požadavky, zobrazí se v okně PowerShell upozornění. Před instalací agenta Nezapomeňte tyto [požadavky](how-to-connect-health-agent-install.md#requirements) dokončit. Následující snímek obrazovky ukazuje příklad těchto upozornění.

![Snímek obrazovky zobrazující Azure AD Connect Health AD FS konfiguraci skriptu](./media/how-to-connect-health-agent-install/install4.png)

Chcete-li ověřit, zda byl agent nainstalován, vyhledejte následující služby na serveru. Pokud jste konfiguraci dokončili, měly by již být spuštěné. V opačném případě se zastaví až do dokončení konfigurace.

* Diagnostické služby AD FS pro Azure AD Connect Health
* Služba analýz AD FS pro Azure AD Connect Health
* Služba monitorování AD FS pro Azure AD Connect Health

![Snímek obrazovky zobrazující Azure AD Connect Health služby AD FS](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Povolit auditování pro AD FS

> [!NOTE]
> Tato část se vztahuje pouze na servery AD FS. Na proxy serverech webových aplikací nemusíte postupovat podle těchto kroků.
>

Funkce analýzy využití potřebuje shromažďovat a analyzovat data. Proto Agent Azure AD Connect Health potřebuje informace v protokolech auditu AD FS. Tyto protokoly nejsou ve výchozím nastavení povolené. Pomocí následujících postupů povolte auditování AD FS a na serverech AD FS vyhledejte AD FS protokoly auditu.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Povolení auditování služby AD FS v systému Windows Server 2012 R2

1. Na obrazovce Start otevřete **Správce serveru** a pak otevřete **místní zásady zabezpečení**. Nebo na hlavním panelu otevřete **Správce serveru** a pak vyberte **nástroje/místní zásady zabezpečení**.
2. Ve složce pro *přiřazení práv k přiřazení uživatelských pro zabezpečení \* použijte. Pak dvakrát klikněte na možnost **Generovat audity zabezpečení**.
3. Na kartě **Místní nastavení zabezpečení** zkontrolujte, jestli je tam uvedený účet služby AD FS. Pokud není v seznamu, vyberte **Přidat uživatele nebo skupinu** a přidejte ho do seznamu. Pak vyberte **OK**.
4. Chcete-li povolit auditování, otevřete okno příkazového řádku se zvýšenými oprávněními. Pak spusťte následující příkaz: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Zavřete **místní zásady zabezpečení**.
    >[!Important]
    >Následující kroky jsou vyžadovány pouze pro primární AD FS servery. 
1. Otevřete modul snap-in **Správa služby AD FS**. (V **Správce serveru** vyberte **nástroje**  >  . **Správa AD FS**.)
1. V podokně **Akce** vyberte **Upravit služba FS (Federation Service) vlastnosti**.
1. V dialogovém okně **vlastnosti služba FS (Federation Service)** vyberte kartu **události** .
1. Zaškrtněte políčka **Úspěšné audity a audity selhání** a pak vyberte **OK**.
1. Pokud chcete povolit podrobné protokolování prostřednictvím PowerShellu, použijte následující příkaz: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Povolení auditování služby AD FS v systému Windows Server 2016

1. Na obrazovce Start otevřete **Správce serveru** a pak otevřete **místní zásady zabezpečení**. Nebo na hlavním panelu otevřete **Správce serveru** a pak vyberte **nástroje/místní zásady zabezpečení**.
2. Přejděte do složky pro *přiřazení práv k přiřazení uživatelských zabezpečení \* a pak dvakrát klikněte na možnost **Generovat audity zabezpečení**.
3. Na kartě **Místní nastavení zabezpečení** zkontrolujte, jestli je tam uvedený účet služby AD FS. Pokud není v seznamu, vyberte **Přidat uživatele nebo skupinu** a přidejte do seznamu účet služby AD FS. Pak vyberte **OK**.
4. Chcete-li povolit auditování, otevřete okno příkazového řádku se zvýšenými oprávněními. Pak spusťte následující příkaz: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Zavřete **místní zásady zabezpečení**.
    >[!Important]
    >Následující kroky jsou vyžadovány pouze pro primární AD FS servery.
1. Otevřete modul snap-in **Správa služby AD FS**. (V **Správce serveru** vyberte **nástroje**  >  . **Správa AD FS**.)
1. V podokně **Akce** vyberte **Upravit služba FS (Federation Service) vlastnosti**.
1. V dialogovém okně **vlastnosti služba FS (Federation Service)** vyberte kartu **události** .
1. Zaškrtněte políčka **Úspěšné audity a audity selhání** a pak vyberte **OK**. Audity úspěchů a audity selhání by měly být ve výchozím nastavení povolené.
1. Otevřete okno PowerShellu a spusťte následující příkaz: 

    `Set-AdfsProperties -AuditLevel Verbose`

Úroveň auditu Basic je ve výchozím nastavení povolená. Další informace najdete v tématu [AD FS vylepšení auditu v systému Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Nalezení protokolů auditu služby AD FS

1. Otevřete **Prohlížeč událostí**.
2. Klikněte na **protokoly systému Windows** a pak vyberte **zabezpečení**.
3. Na pravé straně vyberte **Filtrovat aktuální protokoly**.
4. V případě **zdrojů událostí** vyberte **AD FS auditování**.

    Další informace o protokolech auditu najdete v tématu věnovaném [dotazům na operace](reference-connect-health-faq.md#operations-questions).

    ![Snímek obrazovky zobrazující okno Filtrovat aktuální protokol V poli "zdroje událostí" se vybere položka AD FS audit.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Zásady skupiny můžou zakázat auditování služby AD FS. Pokud je auditování AD FS zakázané, není k dispozici analýza využití informací o aktivitách přihlášení. Ujistěte se, že nemáte žádné zásady skupiny zakazující AD FS auditování.
>


## <a name="install-the-agent-for-sync"></a>Nainstalovat agenta pro synchronizaci

Agent Azure AD Connect Health pro synchronizaci je automaticky nainstalován v nejnovější verzi Azure AD Connect. Pokud chcete použít Azure AD Connect ke synchronizaci, [Stáhněte si nejnovější verzi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) a nainstalujte ji.

Pokud chcete ověřit úspěšnou instalaci agenta, vyhledejte na serveru následující služby. Pokud jste konfiguraci dokončili, měly by být služby již spuštěné. V opačném případě se služby zastaví až do dokončení konfigurace.

* Služba analýz synchronizace služby Azure AD Connect Health
* Služba monitorování synchronizace služby Azure AD Connect Health

![Snímek obrazovky zobrazující běžící Azure AD Connect Health pro synchronizační služby na serveru.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Nezapomeňte, že pro použití Azure AD Connect Health musíte mít Azure AD Premium (P1 nebo P2). Pokud nemáte Azure AD Premium, nemůžete dokončit konfiguraci v Azure Portal. Další informace najdete v tématu [požadavky](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Ruční registrace Azure AD Connect Health pro synchronizaci

Pokud se registrace agenta Azure AD Connect Health pro synchronizaci po úspěšné instalaci Azure AD Connect nezdaří, můžete agenta ručně zaregistrovat pomocí příkazu prostředí PowerShell.

> [!IMPORTANT]
> Tento příkaz PowerShellu použijte jenom v případě, že se registrace agenta po instalaci Azure AD Connect nezdařila.
>
>

Ručně zaregistrujte agenta Azure AD Connect Health pro synchronizaci pomocí následujícího příkazu PowerShellu. Služby Azure AD Connect Health se spustí po úspěšné registraci agenta.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Příkaz přijímá následující parametry:

* **AttributeFiltering**: `$true` (výchozí) Pokud Azure AD Connect nesynchronizuje výchozí sadu atributů a byla přizpůsobena pro použití filtrované sady atributů. V opačném případě použijte `$false` .
* **StagingMode**: `$false` (výchozí), pokud Azure AD Connect Server není  v pracovním režimu. Pokud je server nakonfigurovaný jako v pracovním režimu, použijte `$true` .

Po zobrazení výzvy k ověření použijte stejný účet globálního správce (například admin@domain.onmicrosoft.com ), který jste použili ke konfiguraci Azure AD Connect.

## <a name="install-the-agent-for-azure-ad-ds"></a>Instalace agenta pro Azure služba AD DS

Chcete-li spustit instalaci agenta, poklikejte na stažený soubor *. exe* . V prvním okně vyberte **instalovat**.

![Snímek obrazovky s oknem pro instalaci agenta Azure AD Connect Health služba AD DS](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Po dokončení instalace vyberte **Konfigurovat nyní**.

![Snímek obrazovky zobrazující okno, které dokončuje instalaci agenta Azure AD Connect Health pro Azure služba AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Otevře se okno příkazového řádku. PowerShell se spustí `Register-AzureADConnectHealthADDSAgent` . Až se zobrazí výzva, přihlaste se k Azure.

![Snímek obrazovky zobrazující přihlašovací okno Azure AD Connect Healthho agenta pro Azure služba AD DS](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Po přihlášení pokračuje prostředí PowerShell. Po dokončení můžete PowerShell zavřít. Konfigurace je dokončena.

V tuto chvíli by se měly služby spouštět automaticky, což agentovi umožňuje monitorovat a shromažďovat data. Pokud jste nesplnili všechny požadavky uvedené v předchozích částech, zobrazí se v okně PowerShell upozornění. Před instalací agenta Nezapomeňte tyto [požadavky](how-to-connect-health-agent-install.md#requirements) dokončit. Následující snímek obrazovky ukazuje příklad těchto upozornění.

![Snímek obrazovky s upozorněním na Azure AD Connect Health agenta pro konfiguraci Azure služba AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Chcete-li ověřit, zda je agent nainstalován, vyhledejte v řadiči domény následující služby:

* Služba analýz AD DS pro Azure AD Connect Health
* Služba monitorování AD DS pro Azure AD Connect Health

Pokud jste konfiguraci dokončili, tyto služby by již měly být spuštěny. V opačném případě se zastaví až do dokončení konfigurace.

![Snímek obrazovky zobrazující spuštěné služby na řadiči domény.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Rychlá instalace agenta na více serverech

1. Vytvořte uživatelský účet ve službě Azure AD. Zabezpečte ho pomocí hesla.
2. Přiřaďte roli **vlastníka** pro tento místní účet Azure AD v Azure AD Connect Health pomocí portálu. Postupujte podle [těchto kroků](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Přiřaďte roli ke všem instancím služby. 
3. Pro instalaci si stáhněte soubor MSI *. exe* v místním řadiči domény.
4. Spusťte následující skript. Nahraďte parametry novým uživatelským účtem a jeho heslem. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Po dokončení můžete odebrat přístup k místnímu účtu jedním nebo více následujícími úlohami: 
* Odeberte přiřazení role pro místní účet pro Azure AD Connect Health.
* Otočte heslo pro místní účet. 
* Zakažte místní účet Azure AD.
* Odstraňte místní účet Azure AD.  

## <a name="register-the-agent-by-using-powershell"></a>Registrace agenta pomocí prostředí PowerShell

Po instalaci příslušného souboru agenta *setup.exe* můžete agenta zaregistrovat pomocí následujících příkazů PowerShellu v závislosti na roli. Otevřete okno PowerShellu a spusťte příslušný příkaz:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> K registraci v rámci svrchovaných cloudů použijte následující příkazové řádky:
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


Tyto příkazy přijímají `Credential` jako parametr k dokončení registrace neinteraktivní nebo k dokončení registrace na počítači, na kterém běží jádro serveru. Pamatujte na to, že:
* Můžete zachytit `Credential` proměnnou prostředí PowerShell, která je předána jako parametr.
* Můžete zadat libovolnou identitu služby Azure AD, která má oprávnění k registraci agentů a u kterých *není* povolené vícefaktorové ověřování.
* Ve výchozím nastavení mají globální správci oprávnění registrovat agenty. K provedení tohoto kroku můžete také povolit méně privilegované identity. Další informace najdete v tématu [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurace agentů Azure AD Connect Health k používání proxy serveru HTTP

Azure AD Connect Health agenty můžete nakonfigurovat tak, aby fungovaly s proxy HTTP.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` není podporováno. Agent k vytváření webových požadavků používá System.Net namísto služby Windows HTTP Services.
> * Nakonfigurovaná adresa proxy serveru HTTP se používá k předávání šifrovaných zpráv HTTPS.
> * Ověřené servery proxy (použití HTTPBasic) nejsou podporované.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Změna konfigurace proxy agenta

Pokud chcete nakonfigurovat agenta Azure AD Connect Health, aby používal proxy HTTP, můžete:
* Importujte stávající nastavení proxy serveru.
* Zadejte proxy adresy ručně.
* Vymažte existující konfiguraci proxy serveru.

> [!NOTE]
> Pokud chcete aktualizovat nastavení proxy serveru, musíte restartovat všechny služby agenta Azure AD Connect Health. Spusťte následující příkaz:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Importovat existující nastavení proxy serveru

Můžete importovat nastavení proxy serveru HTTP aplikace Internet Explorer tak, aby agenti Azure AD Connect Health mohli nastavení použít. Na všech serverech, na kterých běží Agent stavu, spusťte následující příkaz PowerShellu:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Nastavení proxy serveru WinHTTP můžete importovat, aby je agenti Azure AD Connect Health mohli používat. Na všech serverech, na kterých běží Agent stavu, spusťte následující příkaz PowerShellu:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Zadat proxy adresy ručně

Můžete ručně zadat proxy server. Na všech serverech, na kterých běží Agent stavu, spusťte následující příkaz PowerShellu:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Tady je příklad: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

V tomto příkladu:
* `address`Nastavení může být název serveru DNS, který bude možné přeložit nebo adresa IPv4.
* Můžete vynechat `port` . Pokud tak učiníte, pak je 443 výchozí port.

#### <a name="clear-the-existing-proxy-configuration"></a>Vymazat existující konfiguraci proxy serveru

Existující konfiguraci proxy serveru můžete vymazat spuštěním následujícího příkazu:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Čtení aktuálního nastavení proxy serveru

Aktuální nastavení proxy můžete přečíst spuštěním následujícího příkazu:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Test připojení k Azure AD Connect Health službě

V některých případech může agent Azure AD Connect Health přijít o připojení ke službě Azure AD Connect Health. Příčiny této ztráty připojení můžou zahrnovat problémy se sítí, problémy s oprávněními a různé další problémy.

Pokud Agent nemůže odesílat data do služby Azure AD Connect Health po dobu delší než dvě hodiny, zobrazí se na portálu následující výstraha: "Health Service data nejsou aktuální." 

Můžete zjistit, jestli ovlivněný agent Azure AD Connect Health může odesílat data do služby Azure AD Connect Health spuštěním následujícího příkazu PowerShellu:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Parametr „role“ v současnosti přijímá následující hodnoty:

* ADFS
* Sync
* PŘIDÁ

> [!NOTE]
> Pokud chcete použít nástroj pro připojení, musíte nejdřív zaregistrovat agenta. Pokud nemůžete dokončit registraci agenta, ujistěte se, že jste splnili všechny [požadavky](how-to-connect-health-agent-install.md#requirements) na Azure AD Connect Health. Připojení je ve výchozím nastavení testováno během registrace agenta.
>
>

## <a name="next-steps"></a>Další kroky

Podívejte se na následující související články:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health operace](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
* [Použití Azure AD Connect Health s využitím Azure služba AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)
