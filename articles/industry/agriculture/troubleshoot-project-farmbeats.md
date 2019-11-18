---
title: Řešení potíží
description: Řešení potíží s Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ad1cb3b08f92923ef45b48d79ad8bbdc3277d370
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131971"
---
# <a name="troubleshooting"></a>Řešení potíží

Následující části popisují běžné problémy s FarmBeats Azure a jejich řešení.

Pokud potřebujete další nápovědu, napište do nás na farmbeatssupport@microsoft.com, do tohoto e-mailu zahrňte soubor Deployer. log.

 Pomocí těchto kroků si stáhněte soubor Deployer. log:

1. Zvýrazněná ikona a v rozevíracím seznamu vyberte možnost **Stáhnout** .

    ![Beats farmy projektu](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. Na další obrazovce zadejte cestu k souboru Deployer. log. Například farmbeats-Deployer. log.

## <a name="sensor-telemetry"></a>Telemetrie senzorů

### <a name="telemetry-not-seen"></a>Telemetrie se nezobrazuje.

**Příznak**: zařízení/senzory jsou nasazené a Vy jste propojili FarmBeats se svým partnerem pro zařízení; Nemůžete ale získat nebo zobrazit data telemetrie na FarmBeats.

**Nápravná opatření**: navštivte Azure Portal a postupujte podle těchto kroků:

1. Přejít do skupiny prostředků FarmBeats data hub.   
2. Vybrat **centrum událostí** (DatafeedEventHubNamespace....)  a vyhledejte počet příchozích zpráv.   
3. V případě, že nejsou k dispozici **žádné příchozí zprávy**, obraťte se na partnera zařízení.  
4. V případě **příchozích zpráv**se obraťte farmbeatssupport@microsoft.com s protokoly datového centra a akcelerátory a zachycená telemetrie.

V [části protokoly](#collect-logs-manually) v dokumentu najdete informace o tom, jak stahovat protokoly.    

### <a name="device-appears-offline"></a>Zařízení se zobrazuje v režimu offline.

**Příznaky**: zařízení jsou nainstalována a propojení FarmBeats s partnerem zařízení. Zařízení jsou online a odesílají data telemetrie, ale jsou v režimu offline.

**Nápravné opatření**: interval vytváření sestav není pro toto zařízení nakonfigurovaný. Obraťte se na výrobce zařízení a nastavte interval hlášení. 

### <a name="error-deleting-a-resource"></a>Chyba při odstraňování prostředku

V této části jsou uvedené běžné chybové scénáře při odstraňování zařízení:  

**Zpráva**: na zařízení se odkazuje v senzorech: k zařízení je přidružen jeden nebo více senzorů. Odstraňte senzory a pak zařízení odstraňte.  

**Význam**: zařízení je přidruženo k několika snímačům nasazeným ve farmě.   

**Nápravná opatření**:  

1. Odstraňte senzory přidružené k zařízení přes akcelerátor.  
2. V případě, že chcete senzory přidružit k jinému zařízení, požádejte partnera zařízení, aby to provede.  
3. Odstraňte zařízení pomocí volání DELETE rozhraní API, které nastaví parametr Force jako true.  

**Zpráva**: na zařízení se odkazuje v zařízeních jako na ParentDeviceId: jako podřízená zařízení se používá jedno nebo víc zařízení, která jsou přidružená k tomuto zařízení. Odstraňte je a pak toto zařízení odstraňte.  

**Význam**: vaše zařízení má přidružená jiná zařízení  

**Nápravná opatření**

1. Odstranit zařízení přidružená k určitému zařízení  
2. Odstraní konkrétní zařízení.  

    > [!NOTE]
    > Zařízení nemůžete odstranit, pokud jsou k němu přidružené senzory. Další informace o tom, jak odstranit přidružené senzory, najdete v kapitole [odstranění senzorů](get-sensor-data-from-sensor-partner.md) v článku získání dat snímače.


## <a name="issues-with-jobs"></a>Problémy s úlohami

### <a name="farmbeats-internal-error"></a>Vnitřní chyba FarmBeats

**Zpráva**: interní chyba FarmBeats, další informace najdete v Průvodci odstraňováním potíží.

**Nápravná opatření**: důvodem může být dočasné selhání datového kanálu. Vytvořte úlohu znovu. Pokud chyba přetrvává, přidejte chybu do příspěvku na fóru FarmBeats nebo kontaktu FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Řešení potíží s akcelerátory

### <a name="access-control"></a>Řízení přístupu

**Při přidávání přiřazení role došlo k chybě.**

**Zpráva**: nenašel se žádný shodný uživatel.

**Nápravná opatření**: Ověřte ID e-mailu, u kterého se pokoušíte provést přiřazení role. ID e-mailu musí být přesně shodné s identifikátorem registrovaným pro daného uživatele ve službě Active Directory. Pokud chyba přetrvává, přidejte chybu do příspěvku na fóru FarmBeats nebo kontaktu FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Nepovedlo se přihlásit k akcelerátoru.

**Zpráva**: Chyba: nemáte oprávnění k volání služby. Kontaktujte správce pro autorizaci.

**Nápravná opatření**: požádejte správce, aby vám schvaloval přístup k nasazení FarmBeats. To se dá udělat tak, že provedete příspěvek rozhraní RoleAssignment API nebo prostřednictvím Access Control v podokně nastavení v akcelerátoru.  

Pokud jste už přidali a čelí tuto chybu, zkuste to znovu tak, že aktualizujete stránku.  Pokud chyba přetrvává, přidejte chybu do příspěvku na fóru FarmBeats nebo kontaktu FarmBeatsSupport@microsoft.com.

![Beats farmy projektu](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Akcelerátor – došlo k neznámé chybě.  

**Zpráva**: Chyba: došlo k neznámé chybě.

**Nápravná opatření**: k tomu dochází, pokud ponecháte stránku nečinný po příliš dlouhou dobu. Aktualizujte stránku.  

Pokud chyba stále přetrvává, přidejte chybu do příspěvku na fóru FarmBeats nebo kontaktu FarmBeatsSupport@microsoft.com

**FarmBeats akcelerátor nezobrazuje nejnovější verzi ani po upgradu FarmBeatsDeployment.**

**Nápravná opatření**: k tomu dochází v důsledku trvalosti pracovního procesu služby v prohlížeči.
Zavřete všechny karty prohlížeče, které mají akcelerátor otevřený a zavřete okno prohlížeče. Spusťte novou instanci prohlížeče a znovu načtěte identifikátor URI akcelerátoru. Tím se načte nová verze akcelerátoru.

## <a name="sentinel-imagery-related-issues"></a>Problémy související se službou Sentinel – obrázek

### <a name="sentinel-wrong-username-or-password"></a>Chybné uživatelské jméno nebo heslo Sentinel

**Zpráva o selhání úlohy**; Pro přístup k tomuto prostředku je vyžadováno úplné ověření.

**Akce opravy**: znovu spusťte instalační program pro upgrade centra dat se správným uživatelským jménem a heslem.

**Nápravné akce**: znovu spustit neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5-7 dní a ověřte, jestli je úloha úspěšná.

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>Ověřovací centrum Sentinel má nesprávnou adresu URL nebo není přístupný. 

**Zpráva o selhání úlohy**: bohužel došlo k nějakému problému. Stránka, ke které jste se pokusili získat přístup, je (dočasně) nedostupná. 

**Nápravná opatření**:
1.  Otevřete adresu URL Sentinel (https://scihub.copernicus.eu/dhus/) v prohlížeči a ověřte, jestli je web přístupný. 
2.  Pokud web není přístupný, podívejte se, jestli brána firewall/podniková síť atd. Tento web blokuje a přijímá potřebné kroky, aby výše uvedené adresy URL bylo povolené. 
3.  Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5-7 dní a ověřte, jestli je úloha úspěšná.  

### <a name="sentinel-server-down-for-maintenance"></a>Vypnutí serveru Sentinel za účelem údržby

**Zpráva o selhání úlohy**: Copernicus otevřít centrum přístupu bude brzy! Omlouváme se za nepříjemnosti. v tuto chvíli provádíme údržbu. Za chvilku budeme zpátky online! 

**Nápravná opatření**:

1.  K tomuto problému může dojít, pokud se na serveru Sentinel provádí nějaké aktivity údržby. 
2.  Pokud dojde k chybě jakékoli úlohy nebo kanálu z výše uvedeného důvodu, odešlete úlohu znovu po nějaké době. 
3.  Uživatel může navštívit https://scihub.copernicus.eu/news/, aby zkontroloval informace o všech plánovaných a neplánovaných aktivitách údržby ověřovacích informací.  
4.  Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5-7 dní a ověřte, jestli je úloha úspěšná.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Bylo dosaženo maximálního počtu připojení Sentinel.

**Zpráva o selhání úlohy**: maximální počet dvou souběžných toků dosažených uživatelem "<username>". 

**Nápravná opatření**
1.  Pokud dojde k chybě jakékoli úlohy s výše uvedeným důvodem, používá se stejný účet Sentinel v jiném nasazení nebo softwaru. 
2.  Uživatel může vytvořit nový účet Sentinel a znovu spustit instalační program pro upgrade centra dat s novým uživatelským jménem a heslem Sentinel.  
3.  Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5-7 dní a ověřte, jestli je úloha úspěšná.

### <a name="sentinel-server-refused-connection"></a>Ověřovací server odmítl připojení. 

**Zpráva o selhání úlohy**:

Server odmítl připojení: http://172.30.175.69:8983/solr/dhus 

**Nápravná opatření**: k tomuto problému může dojít, pokud se na ověřovacím serveru provádí nějaké aktivity údržby. 
1.  Pokud dojde k chybě jakékoli úlohy nebo kanálu z výše uvedeného důvodu, odešlete úlohu znovu po nějaké době. 
2.  Uživatel může navštívit https://scihub.copernicus.eu/news/, aby zkontroloval informace o všech plánovaných a neplánovaných aktivitách údržby ověřovacích informací.  
3.  Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5-7 dní a ověřte, jestli je úloha úspěšná.


## <a name="collect-logs-manually"></a>Shromažďovat protokoly ručně

[Nainstalujte a nasaďte]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) na Průzkumník služby Azure Storage.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Jak shromažďovat protokoly úloh ADF datového centra
1. Přihlášení k https://portal.azure.com
2. V textovém poli **Hledat** vyhledejte skupinu prostředků FarmBeats data hub.

    > [!NOTE]
    > Vyberte skupinu prostředků datového centra, která se zadala v době nasazení FarmBeats.

Na řídicím panelu skupiny prostředků vyhledejte účet úložiště (datahublogs....). Například datahublogsmvxmq  

1.  Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu **účtu úložiště** .
2.  Na stránce (datahubblogs....) vyberte **otevřít v Průzkumníkovi** pro zobrazení otevřené aplikace **Průzkumník služby Azure Storage** .
3.  V levém panelu, (datahubblogs...), **kontejnerech objektů BLOB**vyberte **protokoly úloh**.
4.  Na kartě **úlohy – protokoly** vyberte **Stáhnout**.
5.  Vyberte umístění pro stažení protokolů do místní složky na vašem počítači.
6.  Poslat stažený soubor zip e-mailem na farmbeatssupport@microsoft.com

    ![Beats farmy projektu](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Jak shromažďovat protokoly úloh ADF pro akcelerátory

1.  Přihlášení k https://portal.azure.com
2.  V textovém poli **Hledat** vyhledejte skupinu prostředků akcelerátoru FarmBeats.

    > [!NOTE]
    > Vyberte skupinu prostředků akcelerátoru, která se zadala v době nasazení FarmBeats.

3.  Na řídicím panelu skupiny prostředků vyhledejte úložiště.... účet úložiště. Například storagedop4k
4.  Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu účtu úložiště.
5.  Na stránce (úložiště....) vyberte **otevřít v Průzkumníkovi** a zobrazte otevřené aplikace Průzkumník služby Azure Storage.
6.  Na levém panelu < úložiště....), **kontejnery objektů BLOB**, vyberte **protokoly úloh**.
7.  Na kartě **úlohy – protokoly** vyberte **Stáhnout**.
8.  Vyberte umístění pro stažení protokolů do místní složky na vašem počítači.
9.  Poslat stažený soubor zip e-mailem na farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Jak shromažďovat protokoly aplikačních služeb datového centra

1.  Přihlášení k https://portal.azure.com
2.  V textovém poli **Hledat** vyhledejte skupinu prostředků FarmBeats data hub.

    > [!NOTE]
    > Vyberte skupinu prostředků datového centra, která se zadala v době nasazení FarmBeats.

3.  Ve skupině prostředků vyhledejte účet úložiště (datahublogs....). Například datahublogsmvxmq
4.  Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu **účtu úložiště** .
5.  Na stránce (datahubblogs....) vyberte **otevřít v Průzkumníkovi** pro zobrazení otevřené aplikace **Průzkumník služby Azure Storage** .
6.  V levém panelu, (datahubblogs....), **kontejnerech objektů BLOB**vyberte appinsights-Logs.
7.  Na kartě appinsights-logs vyberte **Stáhnout**.
8.  Vyberte cestu pro stažení protokolů do místní složky na vašem počítači.
9.  Odeslat staženou složku e-mailem do farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>Jak shromažďovat protokoly aplikačních služeb akcelerátoru

1.  Přihlášení k https://portal.azure.com
2.  Ve **vyhledávání**vyhledejte skupinu prostředků akcelerátoru FarmBeats.

    > [!NOTE]
    > Vyberte skupinu prostředků akcelerátoru Farmbeats, která je k dispozici v době nasazování FarmBeats.

3.  Ve skupině prostředků vyhledejte účet úložiště (úložiště....). Například storagedop4k
4.  Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu **účtu úložiště** .
5.  Na stránce (úložiště....) vyberte **otevřít v Průzkumníkovi** a zobrazte otevřené aplikace **Průzkumník služby Azure Storage** .
6.  V levém panelu, (úložiště....), **kontejnery objektů BLOB**vyberte appinsights-Logs.
7.  Na kartě appinsights-logs vyberte **Stáhnout**.
8.  Vyberte umístění pro stažení protokolů do místní složky na vašem počítači.
9.  Odeslat staženou složku e-mailem do farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Známé problémy

## <a name="batch-related-issues"></a>Problémy související se službou Batch

**Chyba**: byla dosažena kvóta účtu Batch účtů služby Batch pro zadané předplatné.

**Nápravná opatření**: Zvyšte kvótu nebo odstraňte nepoužité účty Batch a znovu spusťte nasazení.

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory související problémy

**Chyba**: nepovedlo se aktualizovat požadované nastavení na aplikace Azure AD d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: nedostatečná oprávnění k dokončení operace. Ujistěte se, že nastavení výše jsou pro Aplikace Azure AD správně nakonfigurovaná.

**Význam**: Konfigurace registrace aplikace Azure AD neproběhla správně.  

**Nápravná opatření**: požádejte správce IT (s oprávněním pro čtení tenanta), aby používal náš [skript](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) pro vytvoření registrace aplikace Azure AD. Tento skript se automaticky postará o kroky konfigurace i.

**Chyba**: v tomto tenantovi nelze vytvořit novou aplikaci služby Active Directory "fiktivní": jiný objekt se stejnou hodnotou identifikátorů URI identifikátorů vlastností již existuje.

**Význam**: registrace aplikace Azure AD se stejným názvem už existuje.

**Nápravná opatření**: odstraňte existující registraci aplikace Azure AD nebo ji znovu použijte pro instalaci. Pokud znovu používáte stávající Azure AD, předejte ID aplikace a tajný klíč klienta k instalačnímu programu a znovu nasaďte.

## <a name="inputjson-related-issues"></a>Problémy související se vstupem. JSON

**Při** čtení vstupu ze souboru Input. JSON došlo k chybě.

**Nápravná opatření**: k tomuto problému většinou dochází v důsledku neúspěšného zadání správné vstupní cesty JSON nebo názvu pro instalační program. Proveďte příslušné opravy a znovu nasaďte znovu nasazení.

**Chyba při analýze vstupu JSON**

**Nápravná opatření**: k tomuto problému většinou dochází v důsledku nesprávných hodnot v rámci vstupního souboru JSON. Proveďte příslušné opravy a zkuste nasazení zopakovat.

## <a name="high-cpu-usage"></a>Vysoké využití procesoru

**Chyba**: dostanete e-mailové upozornění odkazující na výstrahu vysokého využití procesoru. 

**Nápravná opatření**: 
1.  Přejít do skupiny prostředků FarmBeats data hub.
2.  Vyberte službu App Service.  
3.  Přejít na horizontální navýšení kapacity (App Service plán) a výběr vhodné [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="next-steps"></a>Další kroky

Pokud pořád čelíte nějakým problémům, kontaktujte nás na našem [fóru podpory](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
