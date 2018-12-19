---
title: Instalace agenta služby Azure AD Connect Health | Dokumentace Microsoftu
description: Toto je stránka služby Azure AD Connect Health, která popisuje instalaci agenta pro službu AD FS a synchronizaci.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: mtillman
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: a7c1941bbb44d4d165b70e032f39129a642d0c65
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605945"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalace agenta služby Azure AD Connect Health
Tento dokument vás provede procesem instalace a konfigurace agentů služby Azure AD Connect Health. Agenty si můžete stáhnout [tady](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Požadavky
Následující tabulka představuje seznam požadavků pro používání služby Azure AD Connect Health.

| Požadavek | Popis |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health je funkcí služby Azure AD Premium a vyžaduje Azure AD Premium. <br /><br />Další informace najdete v článku [Začínáme s Azure AD Premium](../fundamentals/active-directory-get-started-premium.md). <br />Pokud chcete začít používat bezplatnou 30denní zkušební verzi, přečtěte si článek o tom, jak [začít se zkušební verzí](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Abyste mohli Azure AD Connect Health začít používat, musíte být globálním správcem služby Azure AD.  |Ve výchozím nastavení můžou agenty stavu instalovat a konfigurovat jenom globální správci. Bez nich agenty nespustíte, nebudete mít přístup na portál ani nebudete moct v rámci Azure AD Connect Health provádět jakékoli operace. Další informace najdete v článku o [správě adresáře Azure AD](../fundamentals/active-directory-administer.md). <br /><br /> Pomocí řízení přístupu na základě rolí můžete povolit přístup k Azure AD Connect Health i ostatním uživatelům z vaší organizace. Další informace najdete v článku o [řízení přístupu k Azure AD Connect Health na základě rolí](how-to-connect-health-operations.md#manage-access-with-role-based-access-control). <br /><br />**Důležité:** Účet použitý při instalaci agentů musí být pracovní nebo školní účet. Nemůže to být účet Microsoft. Další informace najdete v článku o [registraci do Azure jako organizace](../fundamentals/sign-up-organization.md). |
| Agent Azure AD Connect Health je nainstalovaný na každém cílovém serveru | Azure AD Connect Health kvůli získávání dat a poskytování možností monitorování a analýzy vyžaduje, aby na cílových serverech byli nainstalovaní a nakonfigurovaní agenti služby Health. <br /><br />Pokud například potřebujete získávat data z infrastruktury služby AD FS, musí být agent nainstalovaný na serverech služby AD FS a na proxy serverech webových aplikací. Podobně pro načtení dat ve vaší místní infrastruktuře služby AD DS musí být agent nainstalován na řadičích domény. <br /><br /> |
| Odchozí připojení ke koncovým bodům služby Azure | Agent během instalace a za běhu vyžaduje připojení ke koncovým bodům služby Azure AD Connect Health. Pokud je odchozí připojení blokováno pomocí bran firewall, nezapomeňte do seznamu povolených výjimek přidat následující koncové body: Přečtěte si téma [Odchozí připojení pro koncové body](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints). |
|Odchozí připojení na základě IP adres | Informace o filtrování podle IP adres v branách firewall najdete v článku [Rozsahy IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| Kontrola protokolu SSL je pro odchozí připojení filtrovaná nebo zakázaná | Krok registrace agenta nebo operace nahrávání dat můžou selhat, pokud je na síťové vrstvě povolená kontrola nebo ukončování protokolu SSL pro odchozí připojení. Další informace o [způsobu nastavení kontroly protokolu SSL](https://technet.microsoft.com/library/ee796230.aspx) |
| Porty brány firewall na serveru se spuštěným agentem |Agent vyžaduje, aby následující porty brány firewall byly otevřené. Je to proto, aby agent mohl komunikovat s koncovými body služby Azure AD Health.<br /><br /><li>Port 443 protokolu TCP</li><li>Port 5671 protokolu TCP</li> <br />Další informace o [povolení portů brány firewall](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| Pokud je povoleno rozšířené zabezpečení Internet Exploreru, povolte následující weby |Pokud je povoleno rozšířené zabezpečení Internet Exploreru, musí být na serveru, na který budete agenta instalovat, povoleny následující weby.<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>Federační server vaší organizace, který je pro službu Azure Active Directory důvěryhodný. Příklad: https:\//sts.contoso.com</li> Další informace o [způsobu konfigurace aplikace IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
| Ujistěte se, že je nainstalovaný PowerShell v4.0 nebo novější | <li>Windows Server 2008 R2 se dodává s PowerShellem v2.0, který pro agenta není dostačující. Aktualizujte PowerShell, jak je popsáno níže v části [Instalace agenta na servery se systémem Windows Server 2008 R2](#agent-installation-on-windows-server-2008-r2-servers).</li><li>Windows Server 2012 se dodává s PowerShellem v3.0, který pro agenta není dostačující.  [Aktualizujte](https://www.microsoft.com/download/details.aspx?id=40855) Windows Management Framework.</li><li>Windows Server 2012 R2 a novější se dodávají s dostatečně aktuální verzí PowerShellu.</li>|
|Zákaz FIPS|Agenti Azure AD Connect Health nepodporují FIPS.|

### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Odchozí připojení ke koncovým bodům služby Azure
 Agent během instalace a za běhu vyžaduje připojení ke koncovým bodům služby Azure AD Connect Health. Pokud je odchozí připojení blokováno pomocí bran firewall, nezapomeňte do seznamu povolených výjimek přidat následující koncové body: Další informace o [kontrole odchozího připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)

| Doménové prostředí | Požadované koncové body služby Azure |
| --- | --- |
| Všeobecná veřejnost | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.queue.core.windows.net </li><li>&#42;. servicebus.windows.net – Port: 5671 </li><li>&#42;.table.core.windows.net </li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *Tento koncový bod se používá jenom pro účely zjišťování během registrace.</li> |
| Azure Germany | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.queue.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li><li>&#42;.table.core.cloudapi.de </li><li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de *Tento koncový bod se používá jenom pro účely zjišťování během registrace.</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li><li>&#42;.queue.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.table.core.usgovcloudapi.net </li><li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *Tento koncový bod se používá jenom pro účely zjišťování během registrace.</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Stažení a instalace agenta služby Azure AD Connect Health
* Ověřte, že [splňujete požadavky](how-to-connect-health-agent-install.md#requirements) pro službu Azure AD Connect Health.
* Začínáme s využitím Azure AD Connect Health pro službu AD FS
    * [Stažení agenta Azure AD Connect Health pro službu AD FS](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Pokyny k instalaci](#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* Začínáme s využitím Azure AD Connect Health pro synchronizaci
    * [Stažení a instalace nejnovější verze služby Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771) Agent služby Health pro synchronizaci se nainstaluje jako součást instalace služby Azure AD Connect (verze 1.0.9125.0 nebo vyšší).
* Začínáme s využitím Azure AD Connect Health pro službu AD DS
    * [Stažení agenta služby Azure AD Connect Health pro službu AD DS](https://go.microsoft.com/fwlink/?LinkID=820540)
    * [Pokyny k instalaci](#installing-the-azure-ad-connect-health-agent-for-ad-ds)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalace agenta Azure AD Connect Health pro službu AD FS
> [!NOTE]
> Server AD FS by měl být jiný než váš synchronizační server. Neinstalujte agenta AD FS na synchronizační server.
>

Před instalací se ujistěte, že je název hostitele vašeho serveru AD FS jedinečný a nenachází se ve službě AD FS.
Pokud chcete spustit instalaci agenta, poklikejte na stažený soubor .exe. Na první obrazovce klikněte na Instalovat.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install1.png)

Po dokončení instalace klikněte na Konfigurovat.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install2.png)

Otevře se okno PowerShellu pro zahájení procesu registrace agenta. Po zobrazení výzvy se přihlaste pomocí účtu Azure AD s přístupem k provedení registrace agenta. Ve výchozím nastavení má přístup účet globálního správce.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install3.png)

Po přihlášení bude PowerShell pokračovat. Po dokončení můžete PowerShell zavřít. Konfigurace je hotová.

V tuto chvíli by se měly automaticky spustit služby agenta, které mu umožní bezpečným způsobem nahrát požadovaná data do cloudové služby.

Pokud jste nesplnili všechny předpoklady popsané v předchozích částech, budou se v okně PowerShellu zobrazovat upozornění. Nezapomeňte proto [požadavky](how-to-connect-health-agent-install.md#requirements) splnit ještě před samotnou instalací agenta. Následující snímek obrazovky obsahuje příklad těchto chyb.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install4.png)

Pokud chcete ověřit úspěšnou instalaci agenta, vyhledejte na serveru následující služby. Pokud jste konfiguraci dokončili, měly by již být spuštěné. V opačném případě jsou zastavené, dokud se konfigurace nedokončí.

* Diagnostické služby AD FS pro Azure AD Connect Health
* Služba analýz AD FS pro Azure AD Connect Health
* Služba monitorování AD FS pro Azure AD Connect Health

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalace agenta na servery se systémem Windows Server 2008 R2
Kroky pro servery se systémem Windows Server 2008 R2:

1. Zkontrolujte, jestli server používá aktualizaci Service Pack 1 nebo novější.
2. Pro instalaci agenta vypněte rozšířené zabezpečení IE:
3. Před instalací agenta AD Health nainstalujte na jednotlivé servery Windows PowerShell 4.0. Pokud chcete nainstalovat Windows PowerShell 4.0, postupujte následovně:
   * Pomocí následujícího odkazu na stažení offline instalačního programu nainstalujte [rozhraní Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779).
   * Instalace PowerShellu ISE (z funkcí systému Windows)
   * Nainstalujte [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Nainstalujte na server Internet Explorer verze 10 nebo novější. (Tento krok je požadován službou Health, aby bylo možné provádět ověřování s použitím vašich přihlašovacích údajů správce Azure.)
4. Další informace o instalaci Windows PowerShell 4.0 na server se systémem Windows Server 2008 R2 najdete v článku na wikiwebu [tady](https://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Povolení auditování služby AD FS
> [!NOTE]
> Tato část se vztahuje pouze na servery služby AD FS. Na proxy serverech webových aplikací tyto kroky provádět nemusíte.
>

Aby mohla funkce analýzy využití shromažďovat a analyzovat data, potřebuje agent Azure AD Connect Health informace z protokolů auditu služby AD FS. Tyto protokoly nejsou ve výchozím nastavení povolené. Pokud chcete na svých serverech služby AD FS povolit auditování služby AD FS a najít protokoly auditu služby AD FS, postupujte následovně.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Povolení auditování služby AD FS v systému Windows Server 2008 R2
1. Klikněte na **Start**, najeďte myší na **Programy**, potom na **Nástroje pro správu** a potom klikněte na **Místní zásady zabezpečení**.
2. Přejděte do složky **Security Settings\Local Policies\User Rights Management** a poklikejte na položku **Generovat audity zabezpečení**.
3. Na kartě **Místní nastavení zabezpečení** zkontrolujte, jestli je tam uvedený účet služby AD FS 2.0. Pokud tam není, klikněte na **Přidat uživatele nebo skupinu** a přidejte ho do seznamu. Potom klikněte na **OK**.
4. Pokud chcete povolit auditování, otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Zavřete **místní zásady zabezpečení**.
<br />   -- **Následující kroky se vyžadují pouze pro primární servery AD FS.** -- <br />
6. Otevřete modul snap-in **Správa služby AD FS**. Pokud chcete otevřít snap-in Správa služby AD FS, klikněte na **Start**, najeďte myší na **Programy**, potom na **Nástroje pro správu** a potom klikněte na **Správa služby AD FS 2.0**.
7. V podokně **Akce** klikněte na **Upravit vlastnosti služby FS (Federation Service)**.
8. V dialogovém okně **Vlastnosti služby FS (Federation Service)** klikněte na kartu **Události**.
9. Zaškrtněte políčka **Úspěšné audity** a **Neúspěšné audity**.
10. Klikněte na **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Povolení auditování služby AD FS v systému Windows Server 2012 R2
1. Otevřením **Správce serveru** na úvodní obrazovce nebo Správce serveru na hlavním panelu na ploše otevřete **Místní zásady zabezpečení** a klikněte na **Nástroje/Místní zásady zabezpečení**.
2. Přejděte do složky **Security Settings\Local Policies\User Rights Management** a poklikejte na položku **Generovat audity zabezpečení**.
3. Na kartě **Místní nastavení zabezpečení** zkontrolujte, jestli je tam uvedený účet služby AD FS. Pokud tam není, klikněte na **Přidat uživatele nebo skupinu** a přidejte ho do seznamu. Potom klikněte na **OK**.
4. Pokud chcete povolit auditování, otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz: ```auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable```.
5. Zavřete **místní zásady zabezpečení**.
<br />   -- **Následující kroky se vyžadují pouze pro primární servery AD FS.** -- <br />
6. Otevřete snap-in **Správa služby AD FS** (ve Správci serveru klikněte na Nástroje a potom vyberte správu služby AD FS).
7. V podokně **Akce** klikněte na **Upravit vlastnosti služby FS (Federation Service)**.
8. V dialogovém okně **Vlastnosti služby FS (Federation Service)** klikněte na kartu **Události**.
9. Zaškrtněte políčka **Úspěšné audity a Neúspěšné audity** a klikněte na tlačítko **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Povolení auditování služby AD FS v systému Windows Server 2016
1. Otevřením **Správce serveru** na úvodní obrazovce nebo Správce serveru na hlavním panelu na ploše otevřete **Místní zásady zabezpečení** a klikněte na **Nástroje/Místní zásady zabezpečení**.
2. Přejděte do složky **Security Settings\Local Policies\User Rights Management** a poklikejte na položku **Generovat audity zabezpečení**.
3. Na kartě **Místní nastavení zabezpečení** zkontrolujte, jestli je tam uvedený účet služby AD FS. Pokud tam není, klikněte na **Přidat uživatele nebo skupinu** a přidejte účet služby AD FS do seznamu. Potom klikněte na **OK**.
4. Pokud chcete povolit auditování, otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Zavřete **místní zásady zabezpečení**.
<br />   -- **Následující kroky se vyžadují pouze pro primární servery AD FS.** -- <br />
6. Otevřete snap-in **Správa služby AD FS** (ve Správci serveru klikněte na Nástroje a potom vyberte správu služby AD FS).
7. V podokně **Akce** klikněte na **Upravit vlastnosti služby FS (Federation Service)**.
8. V dialogovém okně **Vlastnosti služby FS (Federation Service)** klikněte na kartu **Události**.
9. Zaškrtněte políčka **Úspěšné audity a Neúspěšné audity** a klikněte na tlačítko **OK**. Tyto možnosti by měly být povolené ve výchozím nastavení.
10. Otevřete okno PowerShellu a spusťte následující příkaz: ```Set-AdfsProperties -AuditLevel Verbose```.

Poznámka: Ve výchozím nastavení je povolena úroveň Basic. Další informace najdete v tématu [Vylepšení auditu služby AD FS ve Windows Serveru 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Nalezení protokolů auditu služby AD FS
1. Otevřete **Prohlížeč událostí**.
2. Přejděte na Protokoly systému Windows a vyberte **Zabezpečení**.
3. Na pravé straně klikněte na **Filtrovat aktuální protokoly**.
4. V části Zdroj události vyberte **Auditování služby AD FS**.

    Stručná [poznámka v nejčastějších dotazech](reference-connect-health-faq.md#operations-questions) k protokolům auditu.

![Protokoly auditu služby AD FS](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Zásady skupiny můžou zakázat auditování služby AD FS. Pokud je auditování služby AD FS zakázané, analýzy využití týkající se aktivit přihlašování nebudou dostupné. Zkontrolujte, že nepoužíváte zásady skupiny, které zakazují auditování služby AD FS. >
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalace agenta Azure AD Connect Health pro synchronizaci
> [!NOTE]
> Synchronizační server by měl být jiný než váš server AD FS. Neinstalujte agenta synchronizace na server AD FS.
>

Agent Azure AD Connect Health pro synchronizaci se v posledním sestavení Azure AD Connect instaluje automaticky. Pokud chcete používat Azure AD Connect pro synchronizaci, stáhněte si nejnovější verzi Azure AD Connect a nainstalujte ji. Nejnovější verzi můžete stáhnout [tady](https://www.microsoft.com/download/details.aspx?id=47594).

Pokud chcete ověřit úspěšnou instalaci agenta, vyhledejte na serveru následující služby. Pokud jste konfiguraci dokončili, měly by již být spuštěné. V opačném případě jsou zastavené, dokud se konfigurace nedokončí.

* Služba analýz synchronizace služby Azure AD Connect Health
* Služba monitorování synchronizace služby Azure AD Connect Health

![Ověření Azure AD Connect Health pro synchronizaci](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Nezapomeňte, že používání Azure AD Connect Health vyžaduje službu Azure AD Premium. Pokud nemáte Azure AD Premium, nemůžete dokončit konfiguraci na webu Azure Portal. Další informace najdete na [stránce s požadavky](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Ruční registrace Azure AD Connect Health pro synchronizaci
Pokud se registrace agenta Azure AD Connect Health pro synchronizaci po úspěšné instalaci Azure AD Connect nepodaří, můžete k ruční registraci agenta použít následující příkaz PowerShellu.

> [!IMPORTANT]
> Použití tohoto příkazu PowerShellu je nutné jenom při neúspěšné registraci agenta po instalaci Azure AD Connect.
>
>

Následující příkaz PowerShellu je nutný jenom v případě neúspěšné registrace agenta stavu po úspěšné instalaci a konfiguraci Azure AD Connect. Služby Azure AD Connect Health se spustí po úspěšné registraci agenta.

Ruční registraci agenta Azure AD Connect Health pro synchronizaci můžete provést pomocí následujícího příkazu PowerShellu:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Příkaz přijímá následující parametry:

* AttributeFiltering: $true (výchozí nastavení) – pokud Azure AD Connect nesynchronizuje výchozí sadu atributů a byl přizpůsoben, aby používal filtrovanou sadu atributů. V opačném případě $false.
* StagingMode: $false (výchozí nastavení) – pokud server Azure AD Connect NENÍ v pracovním režimu, $true, pokud je server nakonfigurovaný, aby byl v pracovní režimu.

Po zobrazení výzvy k ověření použijte stejný účet globálního správce (například admin@domain.onmicrosoft.com), který jste použili ke konfiguraci Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalace agenta Azure AD Connect Health pro službu AD DS
Pokud chcete spustit instalaci agenta, poklikejte na stažený soubor .exe. Na první obrazovce klikněte na Instalovat.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Po dokončení instalace klikněte na Konfigurovat.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Spustí se příkazový řádek následovaný kódem PowerShellu, který provede Register-AzureADConnectHealthADDSAgent. Po zobrazení výzvy k přihlášení k Azure se přihlaste.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Po přihlášení bude PowerShell pokračovat. Po dokončení můžete PowerShell zavřít. Konfigurace je hotová.

V tomto okamžiku by služby měly být automaticky spuštěny, což agentovi umožní sledovat a shromažďovat data. Pokud jste nesplnili všechny předpoklady popsané v předchozích částech, budou se v okně PowerShellu zobrazovat upozornění. Nezapomeňte proto [požadavky](how-to-connect-health-agent-install.md#requirements) splnit ještě před samotnou instalací agenta. Následující snímek obrazovky obsahuje příklad těchto chyb.

![Ověření Azure AD Connect Health pro AD DS](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Pokud chcete ověřit úspěšnou instalaci agenta, vyhledejte na řadiči domény následující služby.

* Služba analýz AD DS pro Azure AD Connect Health
* Služba monitorování AD DS pro Azure AD Connect Health

Pokud jste konfiguraci dokončili, tyto služby by již měly být spuštěny. V opačném případě jsou zastavené, dokud se konfigurace nedokončí.

![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)


## <a name="agent-registration-using-powershell"></a>Registrace agenta pomocí PowerShellu
Po nainstalování odpovídajícího agenta můžete provést krok registrace agenta pomocí následujících příkazů PowerShellu, v závislosti na roli. Otevřete okno PowerShellu a spusťte příslušný příkaz:

```
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Tyto příkazy přijímají jako parametr „Credential“, který určuje, že se registrace dokončí neinteraktivním způsobem na počítači s jádrem serveru.
* Parametr Credential je možné uložit do proměnné PowerShellu, která se předá jako parametr.
* Můžete použít jakoukoli identitu služby Azure AD s přístupem k registraci agentů, u které není povolená služba MFA.
* Ve výchozím nastavení mají přístup k provádění registrace agentů globální správci. Provádění tohoto kroku můžete povolit také dalším méně privilegovaným identitám. Další informace o [Řízení přístupu na základě rolí](how-to-connect-health-operations.md#manage-access-with-role-based-access-control).

```
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurace agentů Azure AD Connect Health k používání proxy serveru HTTP
Agenty Azure AD Connect Health můžete nakonfigurovat, aby používaly proxy server HTTP.

> [!NOTE]
> * Použití příkazu „Netsh WinHttp set ProxyServerAddress“ není podporováno, protože agent používá k vytvoření webových požadavků System.Net místo služby Microsoft Windows HTTP Services.
> * Nakonfigurovaná adresa proxy serveru Http se používá k předávání šifrovaných zpráv protokolu Https.
> * Ověřené servery proxy (použití HTTPBasic) nejsou podporované.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Změna konfigurace proxy serveru agenta stavu
Při konfiguraci agenta Azure AD Connect Health k používání proxy serveru HTTP máte následující možnosti.

> [!NOTE]
> Chcete-li aktualizovat nastavení proxy serveru, je třeba restartovat všechny služby agenta Azure AD Connect Health. Spusťte následující příkaz:<br />
> Restart-Service AdHealth*
>
>

#### <a name="import-existing-proxy-settings"></a>Import existujících nastavení proxy serveru
##### <a name="import-from-internet-explorer"></a>Import z Internet Exploreru
Nastavení proxy serveru HTTP z Internet Exploreru je možné importovat, aby je agenti Azure AD Connect Health mohli používat. Na všech serverech se spuštěným agentem Health spusťte následující příkaz prostředí PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Import ze služby WinHTTP
Nastavení proxy serveru ze služby WinHTTP je možné importovat, aby je agenti Azure AD Connect Health mohli používat. Na všech serverech se spuštěným agentem Health spusťte následující příkaz prostředí PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Ruční zadání adres proxy serverů
Na všech serverech se spuštěným agentem Health můžete ručně určit proxy server spuštěním následujícího příkazu prostředí PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Příklad: *Set-AzureAdConnectHealthProxySettings - HttpsProxyAddress myproxyserver: 443*

* Adresa může být název serveru DNS, který lze přeložit, nebo adresa IPv4.
* Port můžete vynechat. Pokud tento parametr vynecháte, bude jako výchozí vybrán port 443.

#### <a name="clear-existing-proxy-configuration"></a>Vymazání existující konfigurace proxy serveru
Existující konfiguraci proxy serveru můžete vymazat spuštěním následujícího příkazu:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Čtení aktuálního nastavení proxy serveru
Nastavení aktuálně nakonfigurovaného proxy serveru můžete přečíst spuštěním následujícího příkazu:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Test připojení ke službě Azure AD Connect Health
Při provozu může dojít k potížím, které způsobí, že agent Azure AD Connect Health ztratí připojení ke službě Azure AD Connect Health. Patří sem problémy se sítí, problémy s oprávněním a další příčiny.

Pokud agenta nemůže odesílat data do služby Azure AD Connect Health po dobu delší než dvě hodiny, je vyznačené na portálu následující výstraha: "Data služby health Service není aktuální." Ověřit, zda ovlivněný agent Azure AD Connect Health může odesílat data do služby Azure AD Connect Health, můžete spuštěním následujícího příkazu prostředí PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Parametr „role“ v současnosti přijímá následující hodnoty:

* ADFS
* Sync
* PŘIDÁ

> [!NOTE]
> Pokud chcete použít nástroj pro připojení, musíte nejdřív dokončit registraci agenta. Pokud se vám nedaří dokončit registraci agenta, zkontrolujte, jestli jste splnili všechny [požadavky](how-to-connect-health-agent-install.md#requirements) služby Azure AD Connect Health. Tento test připojení se ve výchozím nastavení provádí během registrace agenta.
>
>

## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Operace služby Azure AD Connect Health](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)
