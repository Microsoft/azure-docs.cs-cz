---
title: Nasazení služby Azure AD heslo protection verze preview
description: Nasazení hesel Azure AD protection verze preview zakázat chybná hesla na místě
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6fda373f832d6e24d1252587a19c88b0f464dda6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232283"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Ve verzi Preview: Nasadit ochranu heslem Azure AD

|     |
| --- |
| Ochrana heslem Azure AD a seznamu zakázaných vlastního hesla jsou funkce verze public preview služby Azure Active Directory. Další informace o verze Preview najdete v tématu [doplňkové podmínky použití pro Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Teď, když máme představu o [způsob vynucení ochranu heslem Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md), dalším krokem je naplánovat a provést nasazení.

## <a name="deployment-strategy"></a>Strategie nasazení

Microsoft naznačuje, že všechna nasazení spustit v režimu auditování. Režim auditování je počáteční nastavení výchozí kde hesla můžete nadále nastavit a všechny, které by se zablokovaly, vytvořit položky v protokolu událostí. Jakmile proxy servery a agenty řadiče domény jsou plně nasazené v režimu auditování, by mělo být provedeno regular monitorování. Chcete-li určit, jaké zásady hesel dopad vynucení by mělo na uživatele a prostředí, pokud byla zásada vynucená.

Během fáze auditu najít řada organizací:

* Potřebují ke zlepšení existujících provozních procesů používat bezpečnější hesla.
* Uživatelé jsou zvykli na pravidelně výběr nezabezpečená hesla
* Je třeba informovat o tom, že uživatelům na nadcházející změny ve vynucení zabezpečení, dopad může na ně a je pomohou lépe pochopit, jak si mohli vybrat více zabezpečených hesel.

Jakmile tato funkce byla spuštěna v režimu auditování časového, můžete konfiguraci vynucení překlopit z **Audit** k **vynutit** který vyžaduje víc zabezpečených hesel. Cílených monitorování během této doby je vhodné.

## <a name="known-limitation"></a>Známá omezení

Není o známé omezení ve verzi preview proxy ochrany hesel Azure AD. Použití účty správců klientů, které vyžadují vícefaktorové ověřování není podporováno. Budoucí aktualizace ochrany proxy služby Azure AD heslo bude podporovat registrace proxy serveru s oprávněními správce, které vyžadují vícefaktorové ověřování.

## <a name="single-forest-deployment"></a>Nasazení jedné doménové struktury

Verze preview služby Azure AD ochrana heslem se nasazuje s službu proxy serveru na až dva servery a služba agent nástroje řadiče domény se dá přírůstkově nasadit na všechny řadiče domény v doménové struktuře služby Active Directory.

![Jak spolupracují součásti ochrany hesel Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Stáhnout software

Existují dvě požadované instalační programy pro ochranu heslem Azure AD, který si můžete stáhnout z [stažení softwaru společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Nainstalovat a nakonfigurovat službu Azure AD ochrany heslo proxy

1. Vyberte jeden nebo více serverů k hostování služby Azure AD ochrany heslo proxy.
   * Každý z těchto služeb můžou poskytnout jenom zásady pro hesla pro jednu doménovou strukturu a hostitelský počítač musí být připojený k doméně (kořenové a podřízené jsou rovnoměrně podporované) v této doménové struktuře. Služby Azure AD heslo ochrany proxy ke splnění poslání musí existovat síťové připojení mezi alespoň jeden řadič domény v jednotlivých doménách doménové struktury a Azure AD heslo ochrany Proxy hostitelský počítač.
   * Podporuje se nainstalovat a spustit službu Azure AD ochrany proxy serveru na řadiči domény pro účely testování, ale pak vyžaduje připojení k Internetu.

   > [!NOTE]
   > Verze public preview podporuje maximálně dva (2) proxy servery pro každou doménovou strukturu.

2. Nainstalujte službu Proxy zásady hesla software pomocí balíčku AzureADPasswordProtectionProxy.msi MSI.
   * Instalace softwaru nevyžaduje restartování. Instalace softwaru může automatizovat pomocí standardní MSI postupů, například: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Otevřete okno prostředí PowerShell jako správce.
   * Ochrana heslem Azure AD Proxy softwaru zahrnuje nového modulu prostředí PowerShell s názvem AzureADPasswordProtection. Následující kroky jsou založeny na spuštění různých rutiny z tento modul prostředí PowerShell a předpokládají, že máte otevřený nové okno prostředí PowerShell a naimportovali nového modulu následujícím způsobem:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Instalace softwaru upravuje proměnnou prostředí PSModulePath hostitelský počítač. Aby tato změna se projeví tak, aby modul powershell AzureADPasswordProtection můžete importovat a použít budete muset otevřete nové okno konzoly prostředí PowerShell.

   * Zkontrolujte, zda je služba spuštěná pomocí následujícího příkazu prostředí PowerShell: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Výsledek by měl vytvořit výsledek s položkou **stav** vrácením výsledku "Spuštění".

4. Zaregistrujte k proxy serveru.
   * Po dokončení kroku 3 službu Azure AD ochrany proxy server běží na počítači, ale ještě nemá potřebná pověření ke komunikaci s Azure AD. Registrace ve službě Azure AD je nutný pro povolení této možnosti pomocí `Register-AzureADPasswordProtectionProxy` rutiny prostředí PowerShell. Rutina vyžaduje globální správce přihlašovací údaje pro klienta služby Azure, stejně jako místní oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury. Jakmile byl úspěšný, pro danou proxy služby, další volání `Register-AzureADPasswordProtectionProxy` nadále probíhat úspěšně, ale nejsou potřeba.
      * Rutina může spustit takto:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         Tento příklad funguje jenom v případě aktuálně přihlášeného uživatele je také správcem domény služby Active Directory pro kořenovou doménu. Další možností je zadat přihlašovací údaje potřebné domény prostřednictvím `-ForestCredential` parametr.

   > [!TIP]
   > Může uběhnout významnou (počet sekund) při prvním spuštění této rutiny je pro daného tenanta Azure ještě před dokončením provádění rutiny. Pokud selhání je nahlášeno Tato prodleva by neměla považovat za znepokojující.

   > [!NOTE]
   > Registrace proxy serveru služby Azure AD heslo ochrany je očekáván jednorázové krok v životnost služby. Služba proxy automaticky provede další potřebné maintainenance z tohoto bodu a vyšší. Po jeho byl úspěšný pro danou doménovou strukturu, další volání, Register-AzureADPasswordProtectionProxy' nadále probíhat úspěšně, ale nejsou potřeba.

5. Zaregistrujte doménové struktuře.
   * Místní doménové struktuře služby Active Directory musí být inicializován s potřebné pověření pro komunikaci s Azure pomocí `Register-AzureADPasswordProtectionForest` rutiny prostředí Powershell. Rutina vyžaduje globální správce přihlašovací údaje pro klienta služby Azure, stejně jako místní oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury. Tento krok je spustit jednou pro každou doménovou strukturu.
      * Rutina může spustit takto:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         Tento příklad funguje jenom v případě aktuálně přihlášeného uživatele je také správcem domény služby Active Directory pro kořenovou doménu. Další možností je zadat přihlašovací údaje potřebné domény pomocí parametru - ForestCredential.

         > [!NOTE]
         > Pokud více proxy serverů jsou nainstalovány ve vašem prostředí, nezáleží na tom, jaké konkrétní proxy server, výše uvedené výše uvedený postup se spustí při.

         > [!TIP]
         > Může uběhnout významnou (počet sekund) při prvním spuštění této rutiny je pro daného tenanta Azure ještě před dokončením provádění rutiny. Pokud selhání je nahlášeno Tato prodleva by neměla považovat za znepokojující.

   > [!NOTE]
   > Registrace doménové struktury služby Active Directory musí být jednorázové krok v průběhu životnosti doménové struktury. Agenti řadič domény spuštěn v doménové struktuře automaticky provede další potřebné maintainenance z tohoto bodu a vyšší. Jakmile byl úspěšný pro danou doménovou strukturu, další volání `Register-AzureADPasswordProtectionForest` nadále probíhat úspěšně, ale nejsou potřeba.

6. Volitelné: Konfigurace služby Azure AD heslo ochrany tak, aby naslouchala na určitém portu proxy serveru.
   * RPC přes TCP se používá ochrana hesel Azure AD software agenta řadiče domény na řadičích domény ke komunikaci se službou Azure AD ochrany heslo proxy. Ve výchozím nastavení ochrana heslem Azure AD služba Proxy zásady hesla naslouchá na všechny dostupné dynamické RPC koncový bod. V případě potřeby z důvodu síťové topologie nebo požadavky na bránu firewall, může služba místo toho nakonfigurovaná tak, aby naslouchala na určitém portu TCP.
      * Chcete-li konfigurovat službu spustit pod statický port, použijte `Set-AzureADPasswordProtectionProxyConfiguration` rutiny.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

      * Ke konfiguraci služby pro spuštění pod dynamický port, použijte stejný postup ale nastaven StaticPort zpět na nulu, například:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

   > [!NOTE]
   > Službu Azure AD ochrany proxy server vyžaduje ruční restartování po každé změně konfigurace portů. Není nutné restartovat softwaru služby agenta řadič domény běžící na řadičích domény po provedení změny konfigurace této povahy.

   * Může dotazovat aktuální konfiguraci služby pomocí `Get-AzureADPasswordProtectionProxyConfiguration` rutiny jako na následujícím příkladu:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Instalace služby agenta Azure AD heslo ochrany řadiče domény

* Instalace služby Azure AD heslo ochranu řadič domény agenta služby softwaru pomocí `AzureADPasswordProtectionDCAgent.msi` balíčku MSI:
   * Instalace softwaru vyžadují restart počítače při instalaci a odinstalovat z důvodu požadavek operačního systému, aby knihovny DLL filtru hesel jsou pouze načíst nebo uvolněna při restartování.
   * Podporuje se instalace služby agenta řadiče domény na počítači, který ještě není řadič domény. V takovém případě se spustí službu a spustit, ale jinak bude neaktivní až potom, co se tento počítač je nastaven jako řadič domény.

   Instalace softwaru může automatizovat pomocí standardní MSI postupů, například:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Příkaz msiexec příkladu bude mít za následek okamžitý restart; To můžete vyhnout tak, že zadáte `/norestart` příznak.

Po dokončení instalace na řadiči domény a restartuje, ochrana heslem Azure AD instalace softwaru agenta řadič domény je dokončeno. Požadované, nebo možná není žádná další konfigurace.

## <a name="multiple-forest-deployments"></a>Více nasazení doménové struktury

Neexistují žádné další požadavky pro nasazení služby Azure AD ochrana heslem napříč více doménovými strukturami. Každé doménové struktuře je nezávisle nakonfigurovat, jak je popsáno v části nasazení jedné doménové struktury. Ochrana heslem Proxy každý Azure AD podporuje pouze řadiče domény z doménové struktury, které je připojený k. Tento software ochrany hesel Azure AD v dané doménové struktuře neví, Azure AD heslo ochrany softwaru nasazeného v jiné doménové struktuře bez ohledu na konfigurace důvěryhodnosti služby Active Directory.

## <a name="read-only-domain-controllers"></a>Řadiče domény jen pro čtení

Heslo changes\sets nikdy zpracovat a trvalé na řadičích domény jen pro čtení (RODC); Místo toho tyto se předávají do řadiče domény s možností zápisu. Proto je potřeba nainstalovat software agenta řadič domény do řadičů jen pro čtení.

## <a name="high-availability"></a>Vysoká dostupnost

Hlavním cílem s zajištění vysoké dostupnosti ochrany hesel Azure AD je dostupnost proxy servery, když řadiče domény v doménové struktuře se pokoušíte stáhnout nové zásady nebo jiná data z Azure. Verze public preview podporuje maximálně dva proxy servery pro každou doménovou strukturu. Každý agent, řadič domény používá algoritmus jednoduché styl kruhového dotazování, při rozhodování, které proxy serveru volání a přeskočí přes proxy servery, které nereagují.
Obvyklé problémy spojené s vysokou dostupností jsou omezeny návrh softwaru agenta řadiče domény. Řadič domény agent udržuje místní mezipaměti naposledy stažené zásady hesel. I v případě, že všechny registrované proxy servery, které jsou k dispozici z jakéhokoli důvodu, agentů DC nadále vynutit své zásady hesla uložená v mezipaměti. Frekvence přiměřené aktualizace pro zásady pro hesla ve velkém rozsahu je obvykle v pořadí dní, nejsou hodiny nebo méně.   Proto krátké výpadky proxy serverů nezpůsobí významný dopad na fungování funkce ochrany hesla služby Azure AD nebo jeho výhody zabezpečení.

## <a name="next-steps"></a>Další postup

Teď, když jste nainstalovali služby nezbytné pro ochranu heslem Azure AD na místních serverech dokončení [po instalaci, konfiguraci a shromáždění hlášení](howto-password-ban-bad-on-premises-operations.md) nasazení dokončit.

[Koncepční přehled ochrany hesel Azure AD](concept-password-ban-bad-on-premises.md)
