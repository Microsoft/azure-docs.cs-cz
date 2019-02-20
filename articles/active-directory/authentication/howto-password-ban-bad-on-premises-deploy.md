---
title: Nasazení hesel služby Azure AD Protection ve verzi preview
description: Ochrana hesel Azure AD ve verzi preview se zakázat chybná hesla v místním nasazení
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8d39b614c373c63cf1405c5db0f64581c481d1f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417193"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Verze Preview: Nasazení ochrany hesel Azure AD

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Když teď máme představu o [uplatnění Azure AD hesla Protection pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md), dalším krokem je naplánovat a spustit nasazení.

## <a name="deployment-strategy"></a>Strategie nasazení

Microsoft navrhuje, že každého nasazení se spustí v režimu auditování. Režim auditování je počáteční nastavení výchozí kde hesla můžete nadále nastavit a některé, které by se zablokovaly vytvořit položky v protokolu událostí. Nasadíte servery proxy a agentů DC jsou plně v režimu auditování a pravidelně monitorování by mělo být provedeno za účelem zjištění, jaké zásady hesel dopad vynucení by měla na uživatele a prostředí Pokud byla zásada vynucená.

Během fáze auditu najít mnoho organizací:

* Potřebují ke zlepšení existující provozní procesy, použijte více zabezpečených hesel.
* Uživatelé jsou zvyklí pravidelně výběr nezabezpečených hesel
* Potřebují k informování uživatelů o tuto chystanou změnu vynucení zabezpečení, dopad může mít na nich a jim pomůžou lépe pochopit, jak, si mohou vybrat víc zabezpečených hesel.

Po funkci byl spuštěný v režimu auditování rozumné době, můžete konfiguraci vynucení překlopit z **auditu** k **vynutit** a které vyžadují víc zabezpečených hesel. Cílené monitorování během této doby je vhodné.

## <a name="deployment-requirements"></a>Požadavky na nasazení

* Všechny řadiče domény nainstalovanou službu agenta ochrany řadič domény Azure AD hesla musí běžet Windows Server 2012 nebo novější.
* Všechny počítače s nainstalovanou službu Proxy ochrana hesel Azure AD musí běžet Windows Server 2012 R2 nebo novější.
* Všechny počítače, kde jsou nainstalované komponenty ochrana hesel Azure AD, včetně řadičů domény musí mít Universal C runtime nainstalovaný.
Nejlépe to provádí plně opravy počítače prostřednictvím služby Windows Update. V opačném případě může být vhodné balíček aktualizací specifické pro operační systém nainstalovaný – viz [aktualizace pro Universal C Runtime ve službě Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Připojení k síti musí existovat alespoň jeden řadič domény v každé doméně až alespoň jeden server, který hostuje službu Proxy ochrana hesel Azure AD. Toto připojení musí umožňovat řadič domény pro přístup k port mapovač koncových bodů RPC (135) a port serveru RPC na službu proxy serveru. Port serveru RPC je ve výchozím nastavení dynamický port vzdáleného volání Procedur, ale dá se (viz níže) pro použití statického portu.
* Všechny počítače, který je hostitelem služby Proxy ochrana hesel Azure AD musíte mít přístup k síti pro následující koncové body:

    |Koncový bod |Účel|
    | --- | --- |
    |`https://login.microsoftonline.com`|Požadavky na ověření|
    |`https://enterpriseregistration.windows.net`|Funkce Azure AD ochrana heslem|

* Chcete-li povolit odchozí přenosy protokolu TLS 1.2 HTTP nastavené všechny počítače, který je hostitelem služby Proxy ochrana hesel Azure AD.
* Účet globálního správce pro registraci služby Proxy ochrana hesel Azure AD a doménové struktury s Azure AD.
* Účet s oprávněním správce domény služby Active Directory v kořenové doméně doménové struktury pro registraci doménové struktury Windows Server Active Directory s Azure AD.
* Libovolné doméně Active Directory, který je spuštěn řadič domény služby softwaru agenta musíte použít DFSR k replikování adresáře sysvol.

## <a name="single-forest-deployment"></a>Nasazení jedné doménové struktury

Následující diagram znázorňuje, jak základní součásti ochrany hesel služby Azure AD společně v prostředí místní služby Active Directory.

![Jak fungují společně součásti ochrany hesel služby Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Před nasazením je vhodné zkontrolovat, jak software funguje; Podrobnosti najdete na [koncepční přehled služby Azure AD ochrana heslem](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Stáhnout software

Existují dva instalační programy požadovaných pro ochranu hesel služby Azure AD, který si můžete stáhnout z [stažení softwaru společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Instalace a konfigurace služby proxy ochrana hesel Azure AD

1. Vyberte jeden nebo více serverů pro hostování služby Proxy ochrana hesel Azure AD.
   * Každé takové služby můžete pouze zadat zásady pro hesla pro jednu doménovou strukturu a hostitelského počítače musí být připojený k doméně (kořenové a podřízené domény jsou podporované) v této doménové struktuře. V pořadí pro službu Proxy ochrana hesel Azure AD ke splnění své poslání musí existovat síťové připojení mezi aspoň jeden řadič domény v jednotlivých doménách doménové struktury a počítačem Proxy ochrana hesel Azure AD.
   * Je možné nainstalovat a spustit službu Proxy ochrana hesel Azure AD na řadiči domény pro testovací účely; Nevýhodou je, že řadič domény pak vyžaduje připojení k Internetu, které mohou být potíže se zabezpečením. Společnost Microsoft doporučuje, že tato konfigurace se použít jenom pro účely testování.
   * Pro účely redundance, doporučuje se alespoň dva proxy servery. [Zobrazit vysoké dostupnosti](howto-password-ban-bad-on-premises-deploy.md#high-availability)

2. Nainstalujte službu Proxy ochrana hesel Azure AD s použitím balíčku AzureADPasswordProtectionProxySetup.msi MSI.
   * Instalace softwaru nevyžaduje restartování. Instalace softwaru může možné automatizovat pomocí standardní postupy MSI, například: `msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > Před instalací balíčku AzureADPasswordProtectionProxySetup.msi MSI musí běžet služba brány Windows Firewall, jinak dojde k chybě instalace. Pokud brána Windows Firewall je nakonfigurovaná a nebudou tedy spuštěny, alternativním řešením je dočasně povolit a spustit službu brány Windows Firewall během procesu instalace. Proxy software nemá žádné konkrétní závislost na software brány Windows Firewall po instalaci. Pokud používáte bránu firewall jiného dodavatele, ho musí být nakonfigurován tak, aby byly splněny požadavky na nasazení (Povolit přístup k portu 135 pro příchozí připojení a proxy server RPC port serveru, jestli dynamická nebo statická). [Projděte si požadavky na nasazení](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Otevřete okno Powershellu jako správce.
   * Proxy ochrana hesel Azure AD software obsahuje nový modul prostředí PowerShell s názvem AzureADPasswordProtection. Následující kroky jsou založeny na spuštění různé rutiny z tohoto modulu prostředí PowerShell a předpokládají, že jste otevřeli nové okno Powershellu a následujícím způsobem importovat nový modul:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Zkontrolujte, zda je služba spuštěná, pomocí následujícího příkazu Powershellu: `Get-Service AzureADPasswordProtectionProxy | fl`.
     Výsledkem mohou být vráceny výsledky s **stav** vrácením výsledku "Spuštěno".

4. Registrace proxy serveru.
   * Po dokončení kroku 3 Proxy ochrana hesel Azure AD služba běží na počítači, ale ještě nemá potřebné přihlašovací údaje ke komunikaci s Azure AD. Registrace ve službě Azure AD je potřeba povolit, že při použití možnosti `Register-AzureADPasswordProtectionProxy` rutiny Powershellu. Rutina vyžaduje globální správce přihlašovacích údajů pro tenanta služby Azure, stejně jako místní oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury. Po úspěšném pro daný server proxy služby, další vyvolání `Register-AzureADPasswordProtectionProxy` nadále probíhat úspěšně, ale nejsou potřeba.

      Rutinu Register-AzureADPasswordProtectionProxy podporuje tři režimy různé ověřování takto.

      * Režim interaktivní ověřování:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Tento režim nebude fungovat v operačních systémech jádra serveru. Místo toho použijte jeden z režimů ověřování alternativní jak je uvedeno níže.

         > [!NOTE]
         > V tomto režimu může selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat IESC, registrace proxy serveru, pak znovu povolit IESC.

      * Režim ověřování kódu zařízení:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Ověřování můžete dokončit pak podle zobrazených pokynů na jiném zařízení.

      * Režim bezobslužné ověření (založené na heslech):

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Tento režim se nezdaří, pokud ověření z jakéhokoli důvodu vyžadují vícefaktorové ověřování. Pokud je to tento případ, použijte jeden z předchozích dvou režimech k provedení ověřování založený na vícefaktorovém ověřování.

      Aktuálně není potřeba zadat parametr - ForestCredential, která je vyhrazená pro budoucí funkce.

   > [!NOTE]
   > Registrace proxy serveru služby ochrana hesel Azure AD má být jednorázové krok během životnosti služby. Služba proxy automaticky provede všechny potřebné údržby od této chvíle a vyšší. Po proběhla úspěšně pro danou proxy server, další vyvolání "Register-AzureADPasswordProtectionProxy" nadále probíhat úspěšně, ale nejsou potřeba.

   > [!TIP]
   > Může být značné zpoždění (sekund) při prvním spuštění této rutiny pro daného tenanta Azure před dokončením provádění rutiny. Pokud se použije v hlášení selhání Tato prodleva by neměly být zahrnuté znepokojující.

5. Zaregistrujte doménové struktuře.
   * Místní doménové struktuře služby Active Directory je nutné inicializovat s potřebné přihlašovací údaje ke komunikaci s Azure s využitím `Register-AzureADPasswordProtectionForest` rutiny Powershellu. Rutina vyžaduje globální správce přihlašovacích údajů pro tenanta služby Azure, stejně jako místní oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury. Tento krok spustí jednou pro každou doménovou strukturu.

      Rutinu Register-AzureADPasswordProtectionForest podporuje tři režimy různé ověřování takto.

      * Režim interaktivní ověřování:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Tento režim nebude fungovat v operačních systémech jádra serveru. Místo toho použijte jeden z režimů ověřování alternativní jak je uvedeno níže.

         > [!NOTE]
         > V tomto režimu může selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat IESC, registrace proxy serveru, pak znovu povolit IESC.  

      * Režim ověřování kódu zařízení:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Ověřování můžete dokončit pak podle zobrazených pokynů na jiném zařízení.

      * Režim bezobslužné ověření (založené na heslech):
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Tento režim se nezdaří, pokud ověření vyžaduje vícefaktorové ověřování. Pokud je to tento případ, použijte jeden z předchozích dvou režimech k provedení ověřování založený na vícefaktorovém ověřování.

      Výše uvedených příkladech bude úspěšné pouze v případě, že aktuálně přihlášeného uživatele je také správcem domény služby Active Directory pro kořenovou doménu. Pokud to není tento případ, může poskytnout alternativní pověření pomocí parametru - ForestCredential.

   > [!NOTE]
   > Pokud více proxy serverů jsou nainstalovány ve vašem prostředí, nezáleží na tom, které proxy serveru se použije k registraci doménové struktury.

   > [!TIP]
   > Může být značné zpoždění (sekund) při prvním spuštění této rutiny pro daného tenanta Azure před dokončením provádění rutiny. Pokud se použije v hlášení selhání Tato prodleva by neměly být zahrnuté znepokojující.

   > [!NOTE]
   > Registrace v doménové struktuře služby Active Directory očekává se jednorázové krok v platnosti doménové struktury. Agenti řadiče domény spuštěné v doménové struktuře automaticky provede všechny potřebné maintainenance od této chvíle a vyšší. Po úspěšném pro danou doménovou strukturu, další vyvolání `Register-AzureADPasswordProtectionForest` nadále probíhat úspěšně, ale nejsou potřeba.

   > [!NOTE]
   > Aby `Register-AzureADPasswordProtectionForest` úspěšné aspoň jeden Windows Server 2012 nebo novější domény musí být dostupný řadič domény proxy serveru. Neexistuje však žádný software agenta DC možné instalovat na žádném řadiči domény před tento krok.

6. Konfigurace služby Proxy ochrana hesel Azure AD komunikovat přes proxy server HTTP

   Pokud vaše prostředí vyžaduje použití konkrétní proxy server HTTP pro komunikaci s Azure, můžete to provést takto.

   Vytvořte soubor s názvem `proxyservice.exe.config` soubor `%ProgramFiles%\Azure AD Password Protection Proxy\Service` složka s následujícím obsahem:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Pokud váš proxy server HTTP vyžaduje ověření, přidejte značku useDefaultCredentials následujícím způsobem:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   V obou případech by byly nahrazeny `http://yourhttpproxy.com:8080` adresu a port konkrétní proxy serveru HTTP.

   Pokud váš proxy server HTTP je nakonfigurovaný pomocí zásad autorizace, musí být účtu počítače služby Active Directory počítače hostující službu Proxy ochrana hesel Azure AD udělit přístup.

   Měli zastavit a restartovat službu Proxy ochrana hesel Azure AD po vytvoření nebo aktualizaci `proxyservice.exe.config` souboru.

   Služba Proxy ochrana hesel Azure AD nepodporuje použití konkrétní přihlašovací údaje pro připojení k proxy serveru HTTP.

7. Volitelné: Konfigurace služby Proxy ochrana hesel Azure AD tak, aby naslouchala na určitém portu.
   * RPC přes TCP používá software agenta ochrany DC hesel služby Azure AD na řadičích domény ke komunikaci se službou Proxy ochrana hesel Azure AD. Ve výchozím nastavení Proxy ochrana hesel Azure AD služba naslouchá na všechny dostupné dynamické RPC koncový bod. V případě potřeby z důvodu topologii sítí nebo požadavky na bránu firewall, může služba nakonfigurována místo tak, aby naslouchala na určitém portu TCP.
      * Chcete-li službu spustit pod statický port, použijte `Set-AzureADPasswordProtectionProxyConfiguration` rutiny.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

      * Postup konfigurace služby běžet pod dynamický port, použijte stejný postup ale nastavit StaticPort zpět na nulu, takto:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

   > [!NOTE]
   > Služba Proxy ochrana hesel Azure AD vyžaduje, aby ručním restartování po každé změně v konfiguraci portů. Není nutné restartovat službu software agenta řadič domény běžící na řadičích domény po provedení změny konfigurace z této povaha.

   * Aktuální konfigurace služby může zadávat dotazy pomocí `Get-AzureADPasswordProtectionProxyConfiguration` rutiny jako v následujícím příkladu:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Nainstalujte službu agenta ochrany hesel služby Azure AD řadiče domény

   Pomocí softwaru služby agenta nainstalovat řadič domény ochrana hesel Azure AD `AzureADPasswordProtectionDCAgent.msi` balíčku MSI

   Instalace softwaru vyžadují restart počítače při instalaci a odinstalaci z důvodu požadavku operačního systému, že jsou knihovny DLL filtru hesel pouze načten nebo byla uvolněna při restartování.

   Podporuje se instalace agenta služby řadiče domény na počítači, který dosud není řadičem domény. V takovém případě se spustí službu a spustit, ale jinak se neaktivní až potom, co se tento počítač je povýšen na řadič domény.

   Instalace softwaru může možné automatizovat pomocí standardní postupy MSI, například:

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Výše uvedeného příkladu msiexec příkazu způsobí okamžitý restart; To se můžete vyhnout tak, že zadáte `/norestart` příznak.

Po dokončení instalace na řadiči domény a restartování, instalace softwaru agenta ochrany DC hesel služby Azure AD je dokončena. Žádná další konfigurace je vyžaduje nebo je to možné.

## <a name="multiple-forest-deployments"></a>Více nasazení doménové struktury

Neexistují žádné další požadavky na nasazení napříč více doménovými strukturami ochrana hesel Azure AD. Každé doménové struktuře je nezávisle na sobě nastavená způsobem popsaným v části nasazení jedné doménové struktury. Každý Proxy ochrana hesel Azure AD podporuje pouze řadiče domény, který je připojen k doménové struktury. Ochrana hesel Azure AD softwaru nasazeného v jiné doménové struktuře bez ohledu na konfigurace vztahu důvěryhodnosti služby Active Directory Nepozná software ochrany hesel služby Azure AD v dané doménové struktuře.

## <a name="read-only-domain-controllers"></a>Řadiče domény jen pro čtení

Heslo changes\sets nikdy zpracování a trvale se uloží na řadiče domény jen pro čtení (RODC); Místo toho tyto se předávají do řadiče domény s možností zápisu. Proto není nutné k instalaci softwaru agenta řadiče domény do řadiče jen pro čtení.

## <a name="high-availability"></a>Vysoká dostupnost

Hlavním cílem zajistit vysokou dostupnost služby Protection hesel služby Azure AD je dostupnost proxy servery, když řadiče domény v doménové struktuře se pokoušíte stáhnout nové zásady nebo jiná data z Azure. Každý agent řadič domény používá algoritmus jednoduchého stylu kruhové dotazování, při rozhodování o tom, které proxy serveru k volání a přeskočí přes proxy servery, které nereagují. Pro většinu nasazení plně propojené služby Active Directory s replikací v pořádku (státu directory i adresáři sysvol) proxy servery dva (2) mělo stačit k zajištění dostupnosti a proto včas soubory ke stažení nové zásady a další data. Požadované další proxy server, který jde nasadit jako servery.

Běžné problémy související s vysokou dostupností jsou omezeny návrh softwaru agenta řadiče domény. Řadič domény agent udržuje v místní mezipaměti naposledy stažené zásady hesel. I v případě, že všechny registrované servery proxy přestanou být dostupné z nějakého důvodu, agentů DC i nadále vynucovat zásady jejich hesla uložená v mezipaměti. Četnost přiměřené aktualizace pro zásady pro hesla ve velkém rozsahu je obvykle na pořadí, dnů, nikoli hodin nebo i rychleji. Proto krátké výpadky proxy serverů nezpůsobí závažný dopad na fungování funkce Ochrana hesel Azure AD nebo jeho výhody zabezpečení.

## <a name="next-steps"></a>Další postup

Teď, když jste nainstalovali službu požadovaných pro ochranu hesel služby Azure AD na místních serverech dokončení [po instalaci, konfiguraci a shromažďovat informace o vytváření sestav](howto-password-ban-bad-on-premises-operations.md) k dokončení nasazení.

[Koncepční přehled ochrany hesel služby Azure AD](concept-password-ban-bad-on-premises.md)
