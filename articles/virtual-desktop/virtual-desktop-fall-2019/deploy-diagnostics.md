---
title: Nasazení diagnostického nástroje pro virtuální plochu Windows (Classic) – Azure
description: Postup nasazení nástroje Diagnostika UX pro virtuální plochu Windows (Classic)
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ed010b3eed875c8b14892a588be13a32fbbd95ac
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445087"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Nasazení diagnostického nástroje pro virtuální počítače s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Tady je postup, jak Nástroj pro diagnostiku virtuálních počítačů s Windows umožňuje:

- Vyhledejte diagnostické aktivity (správu, připojení nebo informační kanál) pro jednoho uživatele v průběhu jednoho týdne.
- Shromážděte informace o hostiteli relace pro aktivity připojení z vašeho pracovního prostoru Log Analytics.
- Zkontrolujte podrobnosti o výkonu virtuálního počítače (VM) pro konkrétního hostitele.
- Podívejte se, kteří uživatelé jsou přihlášení k hostiteli relace.
- Odeslat zprávu aktivním uživatelům na konkrétním hostiteli relace.
- Odhlaste uživatele z hostitele relace.

## <a name="prerequisites"></a>Požadavky

Předtím, než budete moci nasadit šablonu Azure Resource Manager pro nástroj, je nutné vytvořit registraci aplikace Azure Active Directory a pracovní prostor Log Analytics. Vy nebo správce k tomu potřebují tato oprávnění:

- Vlastník předplatného Azure
- Oprávnění k vytváření prostředků ve vašem předplatném Azure
- Oprávnění k vytvoření aplikace Azure AD
- Oprávnění vlastníka nebo přispěvatele vzdálené plochy

Tyto dva moduly PowerShellu je potřeba nainstalovat i před zahájením práce:

- [Modul Azure PowerShellu](/powershell/azure/install-az-ps)
- [Modul Azure AD](/powershell/azure/active-directory/install-adv2)

Ujistěte se, že máte ID předplatného připravené pro přihlášení.

Až budete mít všechno v pořádku, můžete vytvořit registraci aplikace Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Vytvoření registrace aplikace Azure Active Directory

V této části se dozvíte, jak pomocí PowerShellu vytvořit aplikaci Azure Active Directory s instančním objektem a získat pro ni oprávnění k rozhraní API.

>[!NOTE]
>Oprávnění rozhraní API jsou virtuální počítače s Windows, Log Analytics a Microsoft Graph rozhraní API se do aplikace Azure Active Directory přidala oprávnění.

1. Otevřete Powershell jako správce.
2. Přihlaste se k Azure pomocí účtu, který má oprávnění vlastníka nebo přispěvatele v předplatném Azure, které byste chtěli použít pro nástroj pro diagnostiku:
   ```powershell
   Login-AzAccount
   ```
3. Přihlaste se k Azure AD pomocí stejného účtu:
   ```powershell
   Connect-AzureAD
   ```
4. Přejít do [úložiště GitHub šablon RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) a spustit skript **CreateADAppRegistrationforDiagnostics.ps1** v PowerShellu.
5.  Když se skript zeptá, že chcete aplikaci pojmenovat, zadejte jedinečný název aplikace.


Po úspěšném spuštění skriptu by se měl ve svém výstupu zobrazit následující věci:

-  Zpráva s potvrzením, že vaše aplikace teď má přiřazení role instančního objektu.
-  ID klienta a tajný klíč klienta, které budete potřebovat při nasazení nástroje diagnostiky.

Teď, když jste zaregistrovali aplikaci, je čas nakonfigurovat Log Analytics pracovní prostor.

## <a name="configure-your-log-analytics-workspace"></a>Konfigurace pracovního prostoru Log Analytics

Pro nejlepší možné prostředí doporučujeme nakonfigurovat Log Analytics pracovní prostor s následujícími čítači výkonu, které vám umožní odvodit příkazy uživatelského prostředí ve vzdálené relaci. Seznam doporučených čítačů s navrhovanými mezními hodnotami najdete v tématu [prahové hodnoty čítače výkonu systému Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Vytvoření pracovního prostoru Azure Log Analytics pomocí prostředí PowerShell

Spuštěním skriptu PowerShellu můžete vytvořit pracovní prostor Log Analytics a nakonfigurovat Doporučené čítače výkonu Windows pro monitorování uživatelského prostředí a výkonu aplikace.

>[!NOTE]
>Pokud už máte vytvořený pracovní prostor Log Analytics, který jste provedli bez skriptu PowerShellu, který chcete použít, přeskočte k [ověření výsledku skriptu v Azure Portal](#validate-the-script-results-in-the-azure-portal).

Spuštění skriptu prostředí PowerShell:

1.  Otevřete PowerShell jako správce.
2.  Přejít do [úložiště GitHub šablon RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) a spustit skript **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** v PowerShellu.
3. Zadejte následující hodnoty parametrů:

    - Pro **ResourceGroupName** zadejte název skupiny prostředků.
    - Pro **LogAnalyticsWorkspaceName** zadejte jedinečný název pro váš pracovní prostor Log Analytics.
    - Do pole **umístění** zadejte oblast Azure, kterou používáte.
    - Zadejte **ID předplatného Azure**, které můžete najít v Azure Portal v části **předplatná**.

4. Zadejte přihlašovací údaje uživatele, který má přístup k delegovanému správci.
5. Přihlaste se k Azure Portal se stejnými přihlašovacími údaji uživatele.
6. Zapište nebo nepamatují ID LogAnalyticsWorkspace pro pozdější verzi.
7. Pokud nastavíte pracovní prostor Log Analytics pomocí skriptu PowerShellu, vaše čítače výkonu by už měly být nakonfigurované a můžete přeskočit k [ověření výsledku skriptu v Azure Portal](#validate-the-script-results-in-the-azure-portal). V opačném případě pokračujte k další části.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Konfigurace čítačů výkonu Windows v existujícím pracovním prostoru Log Analytics

Tato část je určena pro uživatele, kteří chtějí použít stávající pracovní prostor Azure Log Analytics vytvořený bez skriptu PowerShellu v předchozí části. Pokud jste skript nepoužili, je nutné ručně nakonfigurovat Doporučené čítače výkonu systému Windows.

Tady je postup, jak ručně nakonfigurovat Doporučené čítače výkonu:

1. Otevřete internetový prohlížeč a přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce.
2. V dalším kroku přejdete do části **Log Analytics pracovní prostory** , ve kterých si prohlédnete nakonfigurované čítače výkonu systému Windows.
3. V části **Nastavení** vyberte  **Upřesnit nastavení**.
4. Potom přejděte do části   >  **čítače výkonu data systému Windows** a přidejte následující čítače:

    -   Logický disk ( \* ) \\ % volného místa
    -   Logický disk (C:) \\ prům. délka fronty disku
    -   Paměť ( \* ) \\ dostupné v MB
    -   \*Čas procesoru () informace o procesoru \\
    -   Zpoždění vstupu uživatele na relaci ( \* ) \\ Max. zpoždění vstupu

Přečtěte si další informace o čítačích výkonu ve [zdrojích dat výkonu systému Windows a Linux v Azure monitor](../../azure-monitor/agents/data-sources-performance-counters.md).

>[!NOTE]
>Jakékoli další čítače, které nakonfigurujete, se nezobrazí v samotném nástroji pro diagnostiku. Aby se zobrazila v nástroji pro diagnostiku, je nutné nakonfigurovat konfigurační soubor nástroje. Pokyny k tomu, jak to provést s pokročilou správou, budou k dispozici v GitHubu později.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Ověří výsledky skriptu v Azure Portal

Než budete pokračovat v nasazování diagnostického nástroje, doporučujeme, abyste ověřili, že vaše aplikace Azure Active Directory má oprávnění rozhraní API a že váš pracovní prostor Log Analytics má předem nakonfigurované čítače výkonu systému Windows.

### <a name="review-your-app-registration"></a>Kontrola registrace aplikace

Chcete-li zajistit, aby registrace aplikace měla oprávnění rozhraní API:

1. Otevřete prohlížeč a připojte se k [Azure Portal](https://portal.azure.com/) s účtem správce.
2. Přejít na **Azure Active Directory**.
3. Přejít na **Registrace aplikací** a vyberte **všechny aplikace**.
4. Vyhledejte registraci aplikace Azure AD se stejným názvem aplikace, který jste zadali v kroku 5 [Vytvoření registrace aplikace Azure Active Directory](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Kontrola pracovního prostoru Log Analytics

Abyste se ujistili, že váš pracovní prostor Log Analytics má předkonfigurované čítače výkonu systému Windows:

1. V [Azure Portal](https://portal.azure.com/)můžete zkontrolovat nakonfigurované čítače výkonu systému Windows, přejít do **Log Analytics pracovních prostorů** .
2. V části **Nastavení** vyberte **Upřesnit nastavení**.
3. Pak přejdete na   >  **čítače výkonu data Windows**.
4. Ujistěte se, že jsou předem nakonfigurované následující čítače:

   - Logický disk ( \* ) \\ % volné místo: zobrazuje množství volného místa celkového použitelného místa na disku v procentech.
   - Logický disk (C:) \\ prům. délka fronty disku: délka požadavku na přenos disku pro jednotku jazyka C. Hodnota by neměla být větší než 2 pro více než krátkou dobu.
   - Paměť ( \* ) \\ dostupné v MB: dostupná paměť systému v megabajtech.
   - Procesor informace o procesoru ( \* ) \\ čas procesoru: procentuální hodnota uplynulého času, který procesor stráví spuštěním nečinného vlákna.
   - Zpoždění vstupu uživatele na relaci ( \* ) \\ Max. zpoždění vstupu

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Připojení k virtuálním počítačům v pracovním prostoru Log Analytics

Aby bylo možné zobrazit stav virtuálních počítačů, musíte povolit Log Analytics připojení. K připojení virtuálních počítačů použijte tento postup:

1. Otevřete prohlížeč a přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce.
2. Přejít do pracovního prostoru Log Analytics.
3. Na levém panelu v části zdroje dat pracovního prostoru vyberte **virtuální počítače**.
4. Vyberte název virtuálního počítače, ke kterému se chcete připojit.
5. Vyberte **Connect** (Připojit).

## <a name="deploy-the-diagnostics-tool"></a>Nasazení diagnostických nástrojů

Nasazení šablony Azure Resource Management pro nástroj pro diagnostiku:

1.  Přejít na [stránku Azure na webu GitHub RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Nasaďte šablonu do Azure a postupujte podle pokynů v šabloně. Ujistěte se, že máte k dispozici následující informace:

    -   Client-Id
    -   Client-Secret
    -   ID pracovního prostoru služby Log Analytics

3.  Až budou zadané vstupní parametry, přijměte podmínky a ujednání a pak vyberte **koupit**.

Nasazení bude trvat 2 – 3 minuty. Po úspěšném nasazení přejdete do skupiny prostředků a zajistěte, aby existovaly prostředky Web App a App Service.

Potom je potřeba nastavit identifikátor URI přesměrování.

### <a name="set-the-redirect-uri"></a>Nastavit identifikátor URI pro přesměrování

Nastavení identifikátoru URI přesměrování:

1.  V [Azure Portal](https://portal.azure.com/)přejděte na **App Services** a vyhledejte aplikaci, kterou jste vytvořili.
2.  Přejít na stránku Přehled a zkopírujte adresu URL, kterou tam najdete.
3.  Přejděte na **Registrace aplikací** a vyberte aplikaci, kterou chcete nasadit.
4.  Na levém panelu v části spravovat oddíl vyberte **ověřování**.
5.  Do textového pole **identifikátor URI pro přesměrování** zadejte požadovaný identifikátor URI pro přesměrování a pak v levém horním rohu nabídky vyberte **Save (Uložit** ).
6. V rozevírací nabídce Typ vyberte **Web** .
7. Zadejte adresu URL na stránce Přehled aplikace a na konec této stránky přidejte **/Security/SignIn-callback** . Příklad: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![Stránka s identifikátorem URI pro přesměrování](../media/redirect-uri-page.png)

8. Teď přejděte k prostředkům Azure, vyberte prostředek Azure App Services s názvem, který jste zadali v šabloně, a přejděte na adresu URL, která k němu je přidružená. (Pokud jste například použili název aplikace, který jste použili v šabloně `contosoapp45` , pak je vaše přidružená adresa URL <http://contoso.azurewebsites.net> ).
9. Přihlaste se pomocí příslušného Azure Active Directory uživatelského účtu.
10.   Vyberte **Přijmout**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuce nástroje pro diagnostiku

Před zpřístupněním diagnostického nástroje pro uživatele se ujistěte, že mají následující oprávnění:

- Uživatelé potřebují pro Log Analytics oprávnění ke čtení. Další informace najdete v tématu [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../../azure-monitor/roles-permissions-security.md).
-  Uživatelé potřebují taky oprávnění ke čtení pro tenanta virtuálních počítačů s Windows (role čtenářů služby Vzdálená plocha). Další informace najdete v tématu [delegovaný přístup na virtuálním počítači s Windows](delegated-access-virtual-desktop-2019.md).

Také musíte uživatelům poskytnout následující informace:

- Adresa URL aplikace
- Názvy skupiny tenantů, ke kterým mají přístup jednotliví klienti.

## <a name="use-the-diagnostics-tool"></a>Použití nástroje pro diagnostiku

Po přihlášení k účtu s použitím informací, které jste obdrželi z vaší organizace, je potřeba hlavní název uživatele (UPN) pro uživatele, pro který chcete zadat dotaz na aktivity. Hledání vám poskytne všechny aktivity pod zadaným typem aktivity, ke kterému došlo během minulého týdne.

### <a name="how-to-read-activity-search-results"></a>Čtení výsledků hledání aktivit

Aktivity jsou seřazené podle časového razítka nejdříve s poslední aktivitou. Pokud výsledky vrátí chybu, nejprve zkontrolujte, zda se jedná o chybu služby. V případě chyb služby vytvořte lístek podpory s informacemi o aktivitě, které nám pomůžou problém ladit. Všechny ostatní typy chyb obvykle mohou vyřešit uživatel nebo správce. Seznam nejběžnějších scénářů chyb a jejich řešení najdete v tématu [identifikace a Diagnostika problémů](diagnostics-role-service-2019.md#common-error-scenarios).

>[!NOTE]
>Chyby služby se nazývají "externí chyby" v odkazované dokumentaci. Tato akce se změní při aktualizaci Reference prostředí PowerShell.

Aktivity připojení můžou mít víc než jednu chybu. Můžete rozbalit typ aktivity a zobrazit všechny chyby, které uživatel provedl. Vyberte název kódu chyby a otevřete tak dialogové okno, ve kterém se zobrazí další informace.

### <a name="investigate-the-session-host"></a>Prozkoumat hostitele relace

Ve výsledcích hledání vyhledejte a vyberte hostitele relace, o které chcete získat informace.

Můžete analyzovat stav hostitele relace:

- Na základě předdefinované prahové hodnoty můžete načíst informace o stavu hostitele relace, které Log Analytics dotazy.
- Pokud není k dispozici žádná aktivita nebo hostitel relace není připojen k Log Analytics, informace nebudou k dispozici.

Můžete také interagovat s uživateli na hostiteli relace:

- Můžete buď odhlásit nebo poslat zprávu přihlášeným uživatelům.
- Uživatel, kterého jste původně hledali, je vybrán ve výchozím nastavení, ale můžete také vybrat další uživatele pro posílání zpráv nebo odhlášení více uživatelů najednou.

### <a name="windows-performance-counter-thresholds"></a>Prahové hodnoty čítače výkonu systému Windows

- Logický disk ( \* ) \\ % volné místo:

    - Zobrazí procentuální hodnotu celkového použitelného místa na logickém disku, který je zdarma.
    - Prahová hodnota: méně než 20% je označeno jako není v pořádku.

- Logický disk (C:) \\ prům. délka fronty disku:

    - Představuje systémové podmínky úložiště.
    - Prahová hodnota: vyšší než 5 je označeno jako není v pořádku.

- Paměť ( \* ) \\ k dispozici v MB:

    - Paměť, která je k dispozici pro systém.
    - Prahová hodnota: méně než 500 MB označeno jako chybné.

- Čas procesoru () informace o procesoru \* \\ :

    - Prahová hodnota: vyšší než 80% je označena jako stav není v pořádku.

- [Zpoždění vstupu uživatele na relaci ( \* ) \\ maximální prodleva vstupu](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - Prahová hodnota: vyšší než 2000 MS je označeno jako není v pořádku.

## <a name="next-steps"></a>Další kroky

- Naučte se monitorovat protokoly aktivit [pomocí Log Analytics diagnostiky](diagnostics-log-analytics-2019.md).
- Přečtěte si o běžných scénářích chyb a o tom, jak je opravit při [identifikaci a diagnostikování problémů](diagnostics-role-service-2019.md).