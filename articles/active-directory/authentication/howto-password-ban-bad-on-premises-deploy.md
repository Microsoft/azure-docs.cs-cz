---
title: Nasazení služby Azure AD hesla protection ve verzi preview
description: Hesla Azure AD protection verze preview se zakázat chybná hesla v místním nasazení
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5774af4e0550ceb7a51e399fcab203a503a7f23f
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033600"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Verze Preview: Nasazení ochrany hesel Azure AD

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Když teď máme představu o [způsob vynucení ochrany hesla Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md), dalším krokem je naplánovat a spustit nasazení.

## <a name="deployment-strategy"></a>Strategie nasazení

Microsoft navrhuje, že každého nasazení se spustí v režimu auditování. Režim auditování je počáteční nastavení výchozí kde hesla můžete nadále nastavit a některé, které by se zablokovaly vytvořit položky v protokolu událostí. Nasadíte servery proxy a agentů DC jsou plně v režimu auditování a pravidelně monitorování by mělo být provedeno za účelem zjištění, jaké zásady hesel dopad vynucení by měla na uživatele a prostředí Pokud byla zásada vynucená.

Během fáze auditu najít mnoho organizací:

* Potřebují ke zlepšení existující provozní procesy, použijte více zabezpečených hesel.
* Uživatelé jsou zvyklí pravidelně výběr nezabezpečených hesel
* Potřebují k informování uživatelů o tuto chystanou změnu vynucení zabezpečení, dopad může mít na nich a jim pomůžou lépe pochopit, jak, si mohou vybrat víc zabezpečených hesel.

Po funkci byl spuštěný v režimu auditování rozumné době, můžete konfiguraci vynucení překlopit z **auditu** k **vynutit** a které vyžadují víc zabezpečených hesel. Cílené monitorování během této doby je vhodné.

## <a name="deployment-requirements"></a>Požadavky na nasazení

* Všechny řadiče domény, kam se agent služby Azure AD hesla ochrany řadiče domény nainstaluje musí běžet Windows Server 2012 nebo novější.

   > [!NOTE]
   > Operační systémy založené na jádro serveru nejsou aktuálně podporovány. Tato podpora je naplánovaná pro zavedení všeobecné dostupnosti

* Všechny počítače s nainstalovaným ochrany hesla Azure AD službu proxy serveru musí běžet Windows Server 2012 R2 nebo novější.

   > [!NOTE]
   > Operační systémy založené na jádro serveru nejsou aktuálně podporovány. Tato podpora je naplánovaná pro zavedení všeobecné dostupnosti

* Všechny počítače, kde jsou nainstalovány součásti ochrany hesla Azure AD, včetně řadičů domény musí mít Universal C runtime nainstalovaný.
Nejlépe to provádí plně opravy počítače prostřednictvím služby Windows Update. V opačném případě může být vhodné balíček aktualizací specifické pro operační systém nainstalovaný – viz [aktualizace pro Universal C Runtime ve službě Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Připojení k síti musí existovat mezi aspoň jeden řadič domény v každé doméně a alespoň jeden server, který hostuje službu Azure AD ochrany heslo proxy serveru. Toto připojení musí umožňovat řadič domény pro přístup k port mapovač koncových bodů RPC (135) a port serveru RPC na službu proxy serveru.  Port serveru RPC je ve výchozím nastavení dynamický port vzdáleného volání Procedur, ale dá se (viz níže) pro použití statického portu.
* Všechny počítače hostující službu proxy služby Azure AD hesla ochrany musí mít přístup k síti pro následující koncové body:

    |Koncový bod |Účel|
    | --- | --- |
    |`https://login.microsoftonline.com`|Požadavky na ověření|
    |`https://enterpriseregistration.windows.net`|Funkce Ochrana hesel Azure AD|

* Účet globálního správce pro registraci služby proxy ochrany hesla Azure AD a doménové struktury s Azure AD.
* Účet s oprávněním správce domény služby Active Directory v kořenové doméně doménové struktury pro registraci doménové struktury Windows Server Active Directory s Azure AD.
* Libovolné doméně Active Directory, který je spuštěn řadič domény služby softwaru agenta musíte použít DFSR k replikování adresáře sysvol.

## <a name="single-forest-deployment"></a>Nasazení jedné doménové struktury

Ochrana hesel Azure AD ve verzi preview se nasazuje s službu proxy serveru na až dva servery a službu agenta DC mohou být nasazeny postupně na všechny řadiče domény v doménové struktuře služby Active Directory.

![Jak spolupracují součásti ochrany hesla Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Stáhnout software

Existují dva instalační programy požadovaných k ochraně heslem služby Azure AD, který si můžete stáhnout z [stažení softwaru společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Instalace a konfigurace proxy serveru služby Azure AD hesla ochrany

1. Vyberte jeden nebo více serverů pro hostování služby Azure AD ochrany heslo proxy serveru.
   * Každé takové služby můžete pouze zadat zásady pro hesla pro jednu doménovou strukturu a hostitelského počítače musí být připojený k doméně (kořene a potomků jsou rovnoměrně podporované) v této doménové struktuře. Služby Azure AD hesla ochrany proxy ke splnění poslání musí existovat síťové připojení mezi aspoň jeden řadič domény v jednotlivých doménách doménové struktury a Azure AD hesla ochrany Proxy hostitelský počítač.
   * Je možné nainstalovat a spustit službu Azure AD ochrany heslo proxy serveru na řadiči domény pro testovací účely, ale řadič domény pak vyžaduje připojení k Internetu.

   > [!NOTE]
   > Verze public preview podporuje maximálně dva (2) servery proxy pro každou doménovou strukturu.

2. Nainstalujte službu Proxy zásady hesla software použitím balíčku AzureADPasswordProtectionProxy.msi MSI.
   * Instalace softwaru nevyžaduje restartování. Instalace softwaru může možné automatizovat pomocí standardní postupy MSI, například: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

      > [!NOTE]
      > Před instalací balíčku AzureADPasswordProtectionProxy.msi MSI musí běžet služba brány Windows Firewall, jinak dojde k chybě instalace. Pokud brána Windows Firewall je nakonfigurovaná a nebudou tedy spuštěny, alternativním řešením je dočasně povolit a spustit službu brány Windows Firewall během procesu instalace. Proxy software nemá žádné konkrétní závislost na software brány Windows Firewall po instalaci. Pokud používáte bránu firewall jiného dodavatele, ho musí být nakonfigurován tak, aby byly splněny požadavky na nasazení (Povolit přístup k portu 135 pro příchozí připojení a proxy server RPC port serveru, jestli dynamická nebo statická). [Projděte si požadavky na nasazení](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Otevřete okno Powershellu jako správce.
   * Ochrana hesel Azure AD Proxy software obsahuje nový modul prostředí PowerShell s názvem AzureADPasswordProtection. Následující kroky jsou založeny na spuštění různé rutiny z tohoto modulu prostředí PowerShell a předpokládají, že jste otevřeli nové okno Powershellu a následujícím způsobem importovat nový modul:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Instalace softwaru změní proměnnou prostředí PSModulePath hostitelský počítač. Aby tato změna se projeví tak, aby modul powershellu AzureADPasswordProtection můžete importovat a používat budete muset otevřít úplně nové okno konzoly Powershellu.

   * Zkontrolujte, zda je služba spuštěná, pomocí následujícího příkazu Powershellu: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Výsledkem mohou být vráceny výsledky s **stav** vrácením výsledku "Spuštěno".

4. Registrace proxy serveru.
   * Po dokončení kroku 3 proxy služby Azure AD hesla ochrany na počítači je spuštěná, ale ještě nemá potřebné přihlašovací údaje ke komunikaci s Azure AD. Registrace ve službě Azure AD je potřeba povolit, že při použití možnosti `Register-AzureADPasswordProtectionProxy` rutiny Powershellu. Rutina vyžaduje globální správce přihlašovacích údajů pro tenanta služby Azure, stejně jako místní oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury. Po úspěšném pro daný server proxy služby, další vyvolání `Register-AzureADPasswordProtectionProxy` nadále probíhat úspěšně, ale nejsou potřeba.
      * Rutina může spustit takto:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         Tento příklad funguje jenom v případě aktuálně přihlášeného uživatele je také správcem domény služby Active Directory pro kořenovou doménu. Další možností je zadat přihlašovací údaje potřebné domény prostřednictvím `-ForestCredential` parametru.

   > [!NOTE]
   > Tato operace může selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat IESC, registrace proxy serveru, pak znovu povolit IESC.

   > [!NOTE]
   > Registrace proxy serveru služby Azure AD hesla ochrany má být jednorázové krok během životnosti služby. Služba proxy automaticky provede všechny potřebné maintainenance od této chvíle a vyšší. Po proběhla úspěšně pro danou doménovou strukturu, další vyvolání "Register-AzureADPasswordProtectionProxy" nadále probíhat úspěšně, ale nejsou potřeba.

   > [!TIP]
   > Může být značné zpoždění (sekund) při prvním spuštění této rutiny pro daného tenanta Azure před dokončením provádění rutiny. Pokud se použije v hlášení selhání Tato prodleva by neměly být zahrnuté znepokojující.

5. Zaregistrujte doménové struktuře.
   * Místní doménové struktuře služby Active Directory je nutné inicializovat s potřebné přihlašovací údaje ke komunikaci s Azure s využitím `Register-AzureADPasswordProtectionForest` rutiny Powershellu. Rutina vyžaduje globální správce přihlašovacích údajů pro tenanta služby Azure, stejně jako místní oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury. Tento krok spustí jednou pro každou doménovou strukturu.
      * Rutina může spustit takto:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         Tento příklad funguje jenom v případě aktuálně přihlášeného uživatele je také správcem domény služby Active Directory pro kořenovou doménu. Další možností je zadat přihlašovací údaje potřebné domény prostřednictvím parametru - ForestCredential.

         > [!NOTE]
         > Tato operace může selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat IESC, registrace proxy serveru, pak znovu povolit IESC.

         > [!NOTE]
         > Pokud více proxy serverů jsou nainstalovány ve vašem prostředí, nezáleží na tom, které není zadán proxy v předchozím postupu.

         > [!TIP]
         > Může být značné zpoždění (sekund) při prvním spuštění této rutiny pro daného tenanta Azure před dokončením provádění rutiny. Pokud se použije v hlášení selhání Tato prodleva by neměly být zahrnuté znepokojující.

   > [!NOTE]
   > Registrace v doménové struktuře služby Active Directory očekává se jednorázové krok v platnosti doménové struktury. Agenti řadiče domény spuštěné v doménové struktuře automaticky provede všechny potřebné maintainenance od této chvíle a vyšší. Po úspěšném pro danou doménovou strukturu, další vyvolání `Register-AzureADPasswordProtectionForest` nadále probíhat úspěšně, ale nejsou potřeba.

   > [!NOTE]
   > Aby `Register-AzureADPasswordProtectionForest` úspěšné aspoň jeden Windows Server 2012 nebo novější domény musí být dostupný řadič domény proxy serveru. Neexistuje však žádný software agenta DC možné instalovat na žádném řadiči domény před tento krok.

6. Volitelné: Konfigurace proxy serveru služby Azure AD hesla ochrany tak, aby naslouchala na určitém portu.
   * RPC přes TCP se používá ochrana hesel Azure AD softwaru agenta řadiče domény na řadičích domény ke komunikaci se službou Azure AD hesla ochrany proxy serveru. Ve výchozím nastavení naslouchá ochrany hesla Azure AD služba Proxy zásady hesla na všechny dostupné dynamické RPC koncový bod. V případě potřeby z důvodu topologii sítí nebo požadavky na bránu firewall, může služba nakonfigurována místo tak, aby naslouchala na určitém portu TCP.
      * Chcete-li službu spustit pod statický port, použijte `Set-AzureADPasswordProtectionProxyConfiguration` rutiny.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

      * Postup konfigurace služby běžet pod dynamický port, použijte stejný postup ale nastavit StaticPort zpět na nulu, takto:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

   > [!NOTE]
   > Proxy služby Azure AD hesla ochrany vyžaduje ruční restartování po každé změně v konfiguraci portů. Není nutné restartovat službu software agenta řadič domény běžící na řadičích domény po provedení změny konfigurace z této povaha.

   * Aktuální konfigurace služby může zadávat dotazy pomocí `Get-AzureADPasswordProtectionProxyConfiguration` rutiny jako v následujícím příkladu:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Instalace agenta služby Azure AD hesla ochranu řadič domény

* Instalace služby Azure AD hesla ochranu řadič domény agenta služby softwaru pomocí `AzureADPasswordProtectionDCAgent.msi` balíčku MSI:
   * Instalace softwaru vyžadují restart počítače při instalaci a odinstalaci z důvodu požadavku operačního systému, že jsou knihovny DLL filtru hesel pouze načten nebo byla uvolněna při restartování.
   * Podporuje se instalace agenta služby řadiče domény na počítači, který dosud není řadičem domény. V takovém případě se spustí službu a spustit, ale jinak se neaktivní až potom, co se tento počítač je povýšen na řadič domény.

   Instalace softwaru může možné automatizovat pomocí standardní postupy MSI, například:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > V ukázkovém příkazu msiexec způsobí okamžitý restart; To se můžete vyhnout tak, že zadáte `/norestart` příznak.

Po dokončení instalace na řadiči domény a restartuje, ochrana heslem služby Azure AD instalace softwaru agenta pro řadič domény je dokončena. Žádná další konfigurace je vyžaduje nebo je to možné.

## <a name="multiple-forest-deployments"></a>Více nasazení doménové struktury

Neexistují žádné další požadavky na nasazení služby Azure AD ochrana heslem napříč více doménovými strukturami. Každé doménové struktuře je nezávisle na sobě nastavená způsobem popsaným v části nasazení jedné doménové struktury. Ochrana heslem Proxy každé služby Azure AD podporuje pouze řadiče domény, který je připojen k doménové struktury. Tento software ochrany hesla Azure AD v dané doménové struktuře nebude vědět o Azure AD hesla ochrana softwaru nasazeném v jiné doménové struktuře bez ohledu na konfigurace vztahu důvěryhodnosti služby Active Directory.

## <a name="read-only-domain-controllers"></a>Řadiče domény jen pro čtení

Heslo changes\sets nikdy zpracování a trvale se uloží na řadiče domény jen pro čtení (RODC); Místo toho tyto se předávají do řadiče domény s možností zápisu. Proto není nutné k instalaci softwaru agenta řadiče domény do řadiče jen pro čtení.

## <a name="high-availability"></a>Vysoká dostupnost

Hlavním cílem zajistit vysokou dostupnost služby protection hesla Azure AD je dostupnost proxy servery, když řadiče domény v doménové struktuře se pokoušíte stáhnout nové zásady nebo jiná data z Azure. Verze public preview podporuje maximálně dva proxy servery pro každou doménovou strukturu. Každý agent řadič domény používá algoritmus jednoduchého stylu kruhové dotazování, při rozhodování o tom, které proxy serveru k volání a přeskočí přes proxy servery, které nereagují.
Běžné problémy související s vysokou dostupností jsou omezeny návrh softwaru agenta řadiče domény. Řadič domény agent udržuje v místní mezipaměti naposledy stažené zásady hesel. I v případě, že všechny registrované servery proxy přestanou být dostupné z nějakého důvodu, agentů DC i nadále vynucovat zásady jejich hesla uložená v mezipaměti. Četnost přiměřené aktualizace pro zásady pro hesla ve velkém rozsahu je obvykle na pořadí, dnů, nikoli hodin nebo i rychleji.   Proto krátké výpadky proxy serverů nezpůsobí závažný dopad na fungování funkce Ochrana hesel Azure AD nebo jeho výhody zabezpečení.

## <a name="next-steps"></a>Další postup

Teď, když jste nainstalovali služeb potřebných k ochraně heslem služby Azure AD na místních serverech dokončení [po instalaci, konfiguraci a shromažďovat informace o vytváření sestav](howto-password-ban-bad-on-premises-operations.md) k dokončení nasazení.

[Koncepční přehled ochrany hesla Azure AD](concept-password-ban-bad-on-premises.md)
