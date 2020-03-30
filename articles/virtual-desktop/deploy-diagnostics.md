---
title: Nasazení diagnostického nástroje pro Virtuální plochu Windows – Azure
description: Jak nasadit nástroj ux diagnostiky pro windows virtual desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123410"
---
# <a name="deploy-the-diagnostics-tool"></a>Nasazení diagnostických nástrojů

>[!IMPORTANT]
>března 2020 jsme dočasně zakázali diagnostické dotazy, které ovlivnily uživatelské prostředí kvůli zvýšené poptávce po službě. To způsobí, že nástroj přestane fungovat, protože spoléhá na tyto dotazy fungovat. Tento článek aktualizujeme, jakmile budou diagnostické dotazy znovu k dispozici.
>
>Do té doby důrazně doporučujeme [použít Log Analytics](diagnostics-log-analytics.md) pro další sledování.

Zde je to, co diagnostický nástroj pro Windows Virtual Desktop může udělat pro vás:

- Vyhledejte diagnostické aktivity (správa, připojení nebo informační kanál) pro jednoho uživatele po dobu jednoho týdne.
- Shromážděte informace o hostiteli relací pro aktivity připojení z pracovního prostoru Log Analytics.
- Zkontrolujte podrobnosti o výkonu virtuálního počítače pro konkrétního hostitele.
- Podívejte se, kteří uživatelé jsou přihlášeni k hostiteli relace.
- Odešle zprávu aktivním uživatelům na určitém hostiteli relace.
- Odhlásit uživatele z hostitele relace.

## <a name="prerequisites"></a>Požadavky

Před nasazením šablony Azure Resource Manager pro tento nástroj je potřeba vytvořit registraci aplikací Azure Active Directory a pracovní prostor Analýzy protokolů. Vy nebo správce k tomu potřebujete tato oprávnění:

- Vlastník předplatného Azure
- Oprávnění k vytváření prostředků ve vašem předplatném Azure
- Oprávnění k vytvoření aplikace Azure AD
- Práva vlastníka nebo přispěvatele RDS

Než začnete, musíte také nainstalovat tyto dva moduly Prostředí PowerShell:

- [Modul Azure PowerShellu](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Modul Azure AD](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Ujistěte se, že máte id předplatného připravené, když se přihlásíte.

Až budete mít všechno v pořádku, můžete vytvořit registraci aplikace Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Vytvoření registrace aplikace Azure Active Directory

V této části se zobrazí postup, jak pomocí PowerShellu vytvořit aplikaci Azure Active Directory s objektem zabezpečení služby a získat pro ni oprávnění rozhraní API.

>[!NOTE]
>Oprávnění rozhraní API jsou Windows Virtual Desktop, Log Analytics a Microsoft Graph API oprávnění jsou přidány do aplikace Azure Active Directory.

1. Otevřete Powershell jako správce.
2. Přihlaste se k Azure pomocí účtu, který má oprávnění vlastníka nebo přispěvatele k předplatnému Azure, které chcete použít pro diagnostický nástroj:
   ```powershell
   Login-AzAccount
   ```
3. Přihlaste se k Azure AD pomocí stejného účtu:
   ```powershell
   Connect-AzureAD
   ```
4. Přejděte na [úložiště GitHub v RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) a spusťte skript **CreateADAppRegistrationforDiagnostics.ps1** v prostředí PowerShell.
5.  Když vás skript požádá o název aplikace, zadejte jedinečný název aplikace.


Po úspěšném spuštění skriptu by měl ve svém výstupu zobrazit následující věci:

-  Zpráva, která potvrzuje, že vaše aplikace má nyní přiřazení role hlavního uživatele služby.
-  ID klienta a tajný klíč klienta, které budete potřebovat při nasazování diagnostického nástroje.

Teď, když jste zaregistrovali aplikaci, je čas nakonfigurovat pracovní prostor Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Konfigurace pracovního prostoru Log Analytics

Pro nejlepší možné prostředí doporučujeme nakonfigurovat pracovní prostor Log Analytics s následujícími čítači výkonu, které umožňují odvodit příkazy uživatelského prostředí ve vzdálené relaci. Seznam doporučených čítačů s navrženými prahovými hodnotami naleznete [v tématu Prahové hodnoty čítačů výkonu systému Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Vytvoření pracovního prostoru Azure Log Analytics pomocí PowerShellu

Můžete spustit skript Prostředí PowerShell k vytvoření pracovního prostoru Analýzy protokolů a nakonfigurovat doporučené čítače výkonu systému Windows pro sledování uživatelského prostředí a výkonu aplikací.

>[!NOTE]
>Pokud už máte existující pracovní prostor Log Analytics, který jste vytvořili bez skriptu PowerShellu, který chcete použít, přeskočte na [ověření výsledků skriptu na webu Azure Portal](#validate-the-script-results-in-the-azure-portal).

Spuštění skriptu prostředí PowerShell:

1.  Otevřete PowerShell jako správce.
2.  Přejděte na [úložiště GitHub v RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) a spusťte skript **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** v prostředí PowerShell.
3. Pro parametry zadejte následující hodnoty:

    - Do **pole ResourceGroupName**zadejte název skupiny prostředků.
    - Do **pole LogAnalyticsWorkspaceName**zadejte jedinečný název pracovního prostoru Log Analytics.
    - V **části Umístění**zadejte oblast Azure, kterou používáte.
    - Zadejte **ID předplatného Azure**, které najdete na webu Azure Portal v části **Předplatná**.

4. Zadejte pověření uživatele s delegovaným přístupem správce.
5. Přihlaste se k portálu Azure pomocí přihlašovacích údajů stejného uživatele.
6. Poznamenejte si nebo si zapamatujte ID LogAnalyticsWorkspace na později.
7. Pokud nastavíte pracovní prostor Log Analytics pomocí skriptu PowerShellu, měly by už být čítače výkonu nakonfigurované a můžete přeskočit na [ověření výsledků skriptu na webu Azure Portal](#validate-the-script-results-in-the-azure-portal). V opačném případě pokračujte k další části.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Konfigurace čítačů výkonu systému Windows ve stávajícím pracovním prostoru Log Analytics

Tato část je pro uživatele, kteří chtějí používat existující pracovní prostor Azure Log Analytics vytvořený bez skriptu PowerShell v předchozí části. Pokud jste skript nepoužili, je nutné ručně nakonfigurovat doporučené čítače výkonu systému Windows.

Tady je postup ruční konfigurace doporučených čítačů výkonu:

1. Otevřete svůj internetový prohlížeč a přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí svého účtu pro správu.
2. Dále přejděte do **pracovních prostorů Analýzy protokolů** a zkontrolujte nakonfigurované čítače výkonu systému Windows.
3. V části **Nastavení** vyberte **Upřesnit nastavení**.
4. Poté přejděte na **datové** > **čítače výkonu systému Windows** a přidejte následující čítače:

    -   Logický\*disk(\\) %Volné místo
    -   LogicalDisk(C:)\\Avg. Délka fronty disku
    -   Paměť(\*\\) Dostupné MBY
    -   Informace o\*\\procesoru( ) Čas procesoru
    -   Zpoždění vstupu uživatele\*na\\relaci( ) Maximální zpoždění vstupu

Další informace o čítačích výkonu ve [zdrojích dat o výkonu Windows a Linuxu najdete ve službě Azure Monitor](/azure/azure-monitor/platform/data-sources-performance-counters).

>[!NOTE]
>Žádné další čítače, které nakonfigurujete, se nezobrazí v samotném diagnostickém nástroji. Chcete-li, aby se zobrazila v diagnostickém nástroji, je třeba nakonfigurovat konfigurační soubor nástroje. Pokyny, jak to udělat s pokročilou správou, budou k dispozici v GitHubu později.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Ověření výsledků skriptu na webu Azure Portal

Než budete pokračovat v nasazování diagnostického nástroje, doporučujeme ověřit, zda vaše aplikace Azure Active Directory má oprávnění rozhraní API a že pracovní prostor Analýzy protokolů má předkonfigurované čítače výkonu systému Windows.

### <a name="review-your-app-registration"></a>Kontrola registrace aplikace

Jak se ujistit, že registrace aplikace má oprávnění rozhraní API:

1. Otevřete prohlížeč a připojte se k [portálu Azure](https://portal.azure.com/) pomocí svého účtu pro správu.
2. Přejděte na **Azure Active Directory**.
3. Přejděte na **Registrace aplikací** a vyberte **Všechny aplikace**.
4. Vyhledejte registraci aplikace Azure AD se stejným názvem aplikace, který jste zadali v kroku 5 [registrace aplikace Azure Active Directory](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Kontrola pracovního prostoru Log Analytics

Chcete-li se ujistit, že pracovní prostor Analýzy protokolů má předkonfigurované čítače výkonu systému Windows:

1. Na [webu Azure Portal](https://portal.azure.com/)přejděte do **pracovních prostorů Analýzy protokolů** a zkontrolujte nakonfigurované čítače výkonu systému Windows.
2. V části **Nastavení**vyberte **Upřesnit nastavení**.
3. Poté přejděte na **data** > **Windows Performance Counters**.
4. Ujistěte se, že jsou předkonfigurovány následující čítače:

   - LogicalDisk(\*\\) %Volné místo: Zobrazuje velikost volného místa celkového využitelného místa na disku v procentech.
   - LogicalDisk(C:)\\Avg. Délka fronty disku: Délka požadavku na přenos disku pro jednotku C. Hodnota by neměla přesáhnout 2 po delší než krátkou dobu.
   - Paměť(\*\\) Dostupné Mbajty: Dostupná paměť pro systém v megabajtech.
   - Informace o\*\\procesoru( ) Čas procesoru: procento uplynulého času, který procesor stráví spuštěním vlákna, který není nečinný.
   - Zpoždění vstupu uživatele\*na\\relaci( ) Maximální zpoždění vstupu

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Připojení k virtuálním počítačům v pracovním prostoru Log Analytics

Aby bylo možné zobrazit stav virtuálních aplikací, budete muset povolit připojení Log Analytics. Podle následujících kroků připojte virtuální počítače:

1. Otevřete prohlížeč a přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí svého účtu pro správu.
2. Přejděte do pracovního prostoru Analýzy protokolů.
3. V levém panelu vyberte v části Zdroje dat pracovního prostoru **virtuální počítače**.
4. Vyberte název virtuálního aplikace, ke kterému se chcete připojit.
5. Vyberte **Connect** (Připojit).

## <a name="deploy-the-diagnostics-tool"></a>Nasazení diagnostických nástrojů

Nasazení šablony Azure Resource Management pro diagnostický nástroj:

1.  Přejděte na [stránku GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Nasaďte šablonu do Azure a postupujte podle pokynů v šabloně. Ujistěte se, že máte k dispozici následující informace:

    -   Id klienta
    -   Tajný klíč klienta
    -   ID pracovního prostoru služby Log Analytics

3.  Jakmile jsou k dispozici vstupní parametry, přijměte podmínky a pak vyberte **Koupit**.

Nasazení bude trvat 2–3 minuty. Po úspěšném nasazení přejděte do skupiny prostředků a ujistěte se, že jsou k dispozici prostředky plánu webové aplikace a služby aplikace.

Poté je třeba nastavit identifikátor URI přesměrování.

### <a name="set-the-redirect-uri"></a>Nastavení identifikátoru URI přesměrování

Nastavení identifikátoru URI přesměrování:

1.  Na [webu Azure Portal](https://portal.azure.com/)přejděte na **App Services** a vyhledejte aplikaci, kterou jste vytvořili.
2.  Přejděte na stránku s přehledem a zkopírujte adresu URL, kterou tam najdete.
3.  Přejděte na **registrace aplikací** a vyberte aplikaci, kterou chcete nasadit.
4.  V levém panelu vyberte v části Spravovat **možnost Ověřování**.
5.  Do textového pole **Uri přesměrování** zadejte požadovaný identifikátor URI přesměrování a v levém horním rohu nabídky vyberte **Uložit.**
6. V rozevírací nabídce v části Typ vyberte **Web.**
7. Zadejte adresu URL ze stránky přehled u aplikace a na její konec přidejte **/security/signin-callback.** Například: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Stránka IDENTIFIKÁTOR URI přesměrování](media/redirect-uri-page.png)

8. Teď přejděte na prostředky Azure, vyberte prostředek Azure App Services s názvem, který jste zadali v šabloně a přejděte na adresu URL, která je k ní přidružená. (Pokud například název aplikace, který jste `contosoapp45`použili v <https://contosoapp45.azurewebsites.net>šabloně, byl , pak je přidružená adresa URL ).
9. Přihlaste se pomocí příslušného uživatelského účtu služby Azure Active Directory.
10.   Vyberte **Přijmout**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuce diagnostického nástroje

Před zpřístupněním diagnostického nástroje uživatelům se ujistěte, že mají následující oprávnění:

- Uživatelé potřebují přístup pro čtení pro analýzu protokolu. Další informace najdete [v tématu Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure Monitoru](/azure/azure-monitor/platform/roles-permissions-security).
-  Uživatelé také potřebují přístup pro čtení pro klienta virtuální plochy Windows (role čtečky RDS). Další informace naleznete [v tématu Delegovaný přístup ve službě Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Uživatelům je také třeba poskytnout následující informace:

- Adresa URL aplikace
- Názvy skupiny klientů jednotlivých tenantů, ke kterým mají přístup.

## <a name="use-the-diagnostics-tool"></a>Použití diagnostického nástroje

Po přihlášení k účtu pomocí informací, které jste obdrželi od vaší organizace, mějte hlavní číslo uživatele připravené pro uživatele, pro kterého chcete dotazovat aktivity. Při hledání se zobrazí všechny aktivity pod zadaným typem aktivity, ke kterému došlo během minulého týdne.

### <a name="how-to-read-activity-search-results"></a>Jak číst výsledky vyhledávání aktivit

Aktivity jsou seřazeny podle časového razítka, přičemž poslední aktivita je první. Pokud výsledky vrátí chybu, nejprve zkontrolujte, zda se jedná o chybu služby. V případě chyb služby vytvořte lístek podpory s informacemi o aktivitě, který nám pomůže problém ladit. Všechny ostatní typy chyb může obvykle vyřešit uživatel nebo správce. Seznam nejběžnějších chybových scénářů a jejich řešení naleznete v [tématu Identifikace a diagnostika problémů](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Chyby služby se v propojené dokumentaci nazývají "externí chyby". To se změní, když aktualizujeme odkaz na prostředí PowerShell.

Aktivity připojení může mít více než jednu chybu. Můžete rozbalit typ aktivity a zobrazit všechny další chyby, se kterými se uživatel setkal. Vyberte název kódu chyby, chcete-li otevřít dialogové okno, chcete-li o něm zobrazit další informace.

### <a name="investigate-the-session-host"></a>Prozkoumat hostitele relace 

Ve výsledcích hledání vyhledejte a vyberte hostitele relace, o kterém chcete získat informace.

Můžete analyzovat stav hostitele relace:

- Na základě předdefinované prahové hodnoty můžete načíst informace o stavu hostitele relace, které se dotazuje log Analytics.
- Pokud není k dispozici žádná aktivita nebo hostitel relací není připojen k Log Analytics, informace nebudou k dispozici.

Můžete také komunikovat s uživateli na hostiteli relace:

- Můžete se odhlásit nebo odeslat zprávu přihlášeným uživatelům.
- Uživatel, kterého jste původně hledali, je vybrán ve výchozím nastavení, ale můžete také vybrat další uživatele, kteří budou odesílat zprávy nebo odhlásit více uživatelů najednou.

### <a name="windows-performance-counter-thresholds"></a>Prahové hodnoty čítače výkonu systému Windows

- Logický\*disk(\\) %Volné místo:

    - Zobrazí procento celkového využitelného místa na logickém disku, který je volný.
    - Prahová hodnota: Méně než 20 % je označeno jako není v pořádku.

- LogicalDisk(C:)\\Avg. Délka fronty disku:

    - Představuje podmínky systému úložiště.
    - Prahová hodnota: Vyšší než 5 je označenjako není v pořádku.

- Paměť(\*\\) Dostupné MBY:

    - Dostupná paměť pro systém.
    - Prahová hodnota: Méně než 500 megabajtů označených jako není v pořádku.

- Informace o\*\\procesoru( ) Čas procesoru:

    - Prahová hodnota: Vyšší než 80 % je označeno jako není v pořádku.

- [Zpoždění vstupu uživatele\*na\\relaci( ) Maximální zpoždění vstupu:](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)

    - Prahová hodnota: Vyšší než 2000 ms je označenjako není v pořádku.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak sledovat protokoly aktivit na [použití diagnostiky pomocí analýzy protokolů](diagnostics-log-analytics.md).
- Přečtěte si o běžných chybových scénářích a o tom, jak je opravit na [adrese Identifikace a diagnostika problémů](diagnostics-role-service.md).
