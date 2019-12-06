---
title: Řešení potíží s Azure FarmBeats
description: Tento článek popisuje, jak řešit potíže s Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0a4fb337adfb2f4e6b8edb86ac620103e929c3a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842131"
---
# <a name="troubleshoot-azure-farmbeats"></a>Řešení potíží s Azure FarmBeats

Tento článek popisuje řešení běžných problémů s FarmBeats Azure.

Pokud chcete získat další nápovědu, kontaktujte nás na adrese farmbeatssupport@microsoft.com. Nezapomeňte do svého e-mailu zahrnout soubor *Deployer. log* .

Pokud chcete stáhnout soubor *Deployer. log* , udělejte toto:

1. Vyberte zvýrazněnou ikonu a potom v rozevíracím seznamu vyberte **Stáhnout**.

    ![FarmBeats projektu](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

1. V dalším podokně zadejte cestu k souboru *Deployer. log* . Zadejte například **farmbeats-Deployer. log**.

## <a name="sensor-telemetry"></a>Telemetrie senzorů

### <a name="cant-view-telemetry-data"></a>Nejde zobrazit data telemetrie.

**Příznak**: zařízení nebo senzory se nasazují a propojení FarmBeats s partnerem zařízení, ale data telemetrie nemůžete získat nebo zobrazit na FarmBeats.

**Nápravná opatření**: 

1. Přejít do skupiny prostředků FarmBeats DataHub.   
1. Vyberte **centrum událostí** (DatafeedEventHubNamespace) a potom zkontrolujte počet příchozích zpráv.
1. Proveďte jednu z následujících akcí:   
   * Pokud neexistují *žádné příchozí zprávy*, obraťte se na partnera zařízení.  
   * Pokud existují *příchozí zprávy*, obraťte se na farmbeatssupport@microsoft.com. Připojte svoje protokoly DataHub a akcelerátory a zachycenou telemetrii.

Pokud chcete pochopit, jak stahovat protokoly, přečtěte si část [shromáždění protokolů ručně](#collect-logs-manually) .  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nemáte připojovací řetězec služby Azure Event Hubs.

**Nápravná opatření**: 

1. V DataHub Swagger navštivte rozhraní API partnera.
1. Vyberte **získat** > **vyzkoušet** > **provést**.
1. Poznamenejte si ID partnera, kterého vás zajímá.
1. Vraťte se do partnerského rozhraní API a vyberte **Get/\<ID >** .
1. Zadejte ID partnera z kroku 3 a pak vyberte **provést**.
   
   Odpověď rozhraní API by měla mít Event Hubs připojovací řetězec.

### <a name="device-appears-offline"></a>Zařízení se zobrazuje v režimu offline.

**Příznaky**: zařízení jsou nainstalována a propojení FarmBeats s partnerem zařízení. Zařízení jsou online a odesílají data telemetrie, ale jsou v režimu offline.

**Nápravné opatření**: interval vytváření sestav není pro toto zařízení nakonfigurovaný. Chcete-li nastavit interval vytváření sestav, obraťte se na výrobce zařízení. 

### <a name="error-deleting-a-device"></a>Chyba při odstraňování zařízení

Při odstraňování zařízení se může zobrazit jeden z následujících běžných scénářů chyb:  

**Zpráva**: na zařízení se odkazuje v senzorech: k zařízení je přidružen jeden nebo více senzorů. Odstraňte senzory a pak zařízení odstraňte. "  

**Význam**: zařízení je přidruženo k několika senzorům, které jsou nasazeny ve farmě.   

**Nápravná opatření**:  

1. Odstraňte senzory, které jsou přidružené k zařízení prostřednictvím akcelerátoru.  
1. Pokud chcete senzory přidružit k jinému zařízení, požádejte partnera zařízení, aby to provede.  
1. Odstraňte zařízení pomocí `DELETE API` volání a nastavte parametr Force na *hodnotu true*.  

**Zpráva**: na zařízení je odkazováno v zařízeních jako ParentDeviceId: k tomuto zařízení jsou přidružená aspoň jedno zařízení jako podřízená zařízení. Odstraňte je a pak toto zařízení odstraňte. "  

To **znamená**, že k zařízení jsou přidružená jiná zařízení.  

**Nápravná opatření**

1. Odstraňte zařízení, která jsou přidružená k tomuto konkrétnímu zařízení.  
1. Odstraňte konkrétní zařízení.  

    > [!NOTE]
    > Zařízení nemůžete odstranit, pokud jsou k němu přidružené senzory. Další informace o tom, jak odstranit přidružené senzory, najdete v části "odstranění senzoru" v tématu [získání dat ze senzorů od partnerů snímačů](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Problémy s úlohami

### <a name="farmbeats-internal-error"></a>Vnitřní chyba FarmBeats

**Zpráva**: "vnitřní chyba FarmBeats, další informace najdete v Průvodci odstraňováním potíží."

**Nápravná opatření**: Tento problém může být způsoben dočasným selháním v datovém kanálu. Vytvořte úlohu znovu. Pokud chyba přetrvává, přidejte chybovou zprávu do příspěvku na fóru FarmBeats nebo kontaktujte FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Řešení potíží s akcelerátory

### <a name="access-control"></a>Řízení přístupu

**Problém**: při přidávání přiřazení role se zobrazí chyba.

**Zpráva**: nebyla nalezena žádná vyhovující uživatelé.

**Nápravná opatření**: Ověřte ID e-mailu, ke kterému se pokoušíte přidat přiřazení role. ID e-mailu musí mít přesnou shodu ID, která je zaregistrovaná pro daného uživatele ve službě Active Directory. Pokud chyba přetrvává, přidejte chybovou zprávu do příspěvku na fóru FarmBeats nebo kontaktujte FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Nepovedlo se přihlásit k akcelerátoru.

**Zpráva**: "Chyba: nemáte oprávnění k volání služby. Kontaktujte správce k autorizaci. "

**Nápravná opatření**: požádejte správce, aby vám schvaloval přístup k nasazení FarmBeats. To se dá udělat tak, že provedete příspěvek rozhraní RoleAssignment API nebo prostřednictvím Access Control v podokně **Nastavení** v akcelerátoru.  

Pokud jste už udělili přístup a tato chyba se týká této chyby, zkuste to znovu tak, že aktualizujete stránku. Pokud chyba přetrvává, přidejte chybovou zprávu do příspěvku na fóru FarmBeats nebo kontaktujte FarmBeatsSupport@microsoft.com.

![FarmBeats projektu](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problémy akcelerátoru  

**Problém**: zobrazila se chyba akcelerátoru neurčité příčiny.

**Zpráva**: "Chyba: došlo k neznámé chybě."

**Nápravná opatření**: k této chybě dochází, pokud ponecháte stránku nečinné příliš dlouho. Aktualizujte stránku.  

Pokud chyba přetrvává, přidejte chybovou zprávu do příspěvku na fóru FarmBeats nebo kontaktujte FarmBeatsSupport@microsoft.com.

**Problém**: akcelerátor FarmBeats nezobrazuje nejnovější verzi, i když jste provedli upgrade FarmBeatsDeployment.

**Nápravná opatření**: k této chybě dochází z důvodu trvalosti pracovního procesu služby v prohlížeči. Udělejte toto:
1. Zavřete všechny karty prohlížeče, které mají otevřený akcelerátor, a zavřete okno prohlížeče. 
1. Spusťte novou instanci prohlížeče a znovu načtěte identifikátor URI akcelerátoru. Tato akce načte novou verzi akcelerátoru.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: problémy související s snímkům

### <a name="wrong-username-or-password"></a>Nesprávné uživatelské jméno nebo heslo

**Zpráva o selhání úlohy**: pro přístup k tomuto prostředku je vyžadováno úplné ověření.

**Nápravná opatření**: 

Proveďte jednu z těchto akcí:
* Spusťte znovu instalační program pro upgrade DataHub se správným uživatelským jménem a heslem.
* Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Ověřovací centrum: nesprávná adresa URL nebo web není přístupný 

**Zpráva o selhání úlohy**: "bohužel došlo k nějakému problému. Stránka, ke které jste se pokusili získat přístup, je (dočasně) nedostupná. 

**Nápravná opatření**:
1. Otevřete v prohlížeči [Sentinel](https://scihub.copernicus.eu/dhus/) a zjistěte, jestli je web přístupný. 
1. Pokud web není přístupný, zkontrolujte, jestli brána firewall, podniková síť nebo jiný blokující software nebrání přístupu k webu, a pak proveďte potřebné kroky, aby se povolila adresa URL ověřovacího připojení. 
1. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.  

### <a name="sentinel-server-down-for-maintenance"></a>Server Sentinel: vypnutí z důvodu údržby

**Zpráva o selhání úlohy**: "centrum přístupu Copernicus otevřené bude brzy! Omlouváme se za nepříjemnosti. v tuto chvíli provádíme údržbu. Brzy se vrátíme do online režimu! " 

**Nápravná opatření**:

K tomuto problému může dojít, pokud se na serveru Sentinel provádí nějaké aktivity údržby.

1. Pokud dojde k chybě nějaké úlohy nebo kanálu, protože probíhá údržba, odešlete úlohu znovu po nějaké době. 

   Informace o všech plánovaných a neplánovaných aktivitách údržby ověřovacích zpráv najdete na webu [Copernicus otevřít centrum Access Hub](https://scihub.copernicus.eu/news/) .  
1. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: dosáhlo se maximálního počtu připojení.

**Zpráva o selhání úlohy**: "maximální počet dvou souběžných toků dosažených uživatelem"\<username > ".

**Význam**: Pokud se úloha nezdařila, protože bylo dosaženo maximálního počtu připojení, je stejný účet Sentinel používán v jiném nasazení softwaru.

**Nápravná opatření**: zkuste jednu z těchto možností:
* Vytvořte nový účet Sentinel a pak znovu spusťte instalační program a upgradujte DataHub pomocí nového uživatelského jména a hesla Sentinel.  
* Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro datum v rozsahu 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.

### <a name="sentinel-server-refused-connection"></a>Ověřovací server: odmítnuté připojení 

**Zpráva o chybě úlohy**: Server odmítl připojení na: http://172.30.175.69:8983/solr/dhus. 

**Nápravná opatření**: k tomuto problému může dojít, pokud se na serveru Sentinel provádí nějaké aktivity údržby. 
1. Pokud dojde k chybě nějaké úlohy nebo kanálu, protože probíhá údržba, odešlete úlohu znovu po nějaké době. 

   Informace o všech plánovaných a neplánovaných aktivitách údržby ověřovacích zpráv najdete na webu [Copernicus otevřít centrum Access Hub](https://scihub.copernicus.eu/news/) .  
1. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.

## <a name="collect-logs-manually"></a>Shromažďovat protokoly ručně

[Nainstalujte a nasaďte Průzkumník služby Azure Storage]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Shromažďování protokolů úloh Azure Data Factory v DataHub
1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Do **vyhledávacího** pole vyhledejte skupinu prostředků FarmBeats DataHub.

    > [!NOTE]
    > Vyberte skupinu prostředků DataHub, kterou jste zadali během nasazování FarmBeats.

1. Na řídicím panelu **skupiny prostředků** vyhledejte *datahublogs\** účet úložiště. Vyhledejte například **datahublogsmvxmq**.  

1. Ve sloupci **název** vyberte účet úložiště pro zobrazení řídicího panelu **účtu úložiště** .
1. V podokně **\*datahubblogs** vyberte **otevřít v Průzkumníkovi** pro zobrazení **otevřené aplikace Průzkumník služby Azure Storage** .
1. V levém podokně vyberte **kontejnery objektů BLOB**a pak vyberte **protokoly úloh**.
1. V podokně **úlohy – protokoly** vyberte **Stáhnout**.
1. Stáhněte protokoly do místní složky na vašem počítači.
1. Odešlete stažený soubor. zip do farmbeatssupport@microsoft.com.

    ![FarmBeats projektu](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Shromažďovat protokoly úloh Azure Data Factory v akcelerátoru 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Do **vyhledávacího** pole vyhledejte skupinu prostředků akcelerátoru FarmBeats.

    > [!NOTE]
    > Vyberte skupinu prostředků akcelerátoru, kterou jste zadali během FarmBeats nasazení.

1. Na řídicím panelu **skupiny prostředků** vyhledejte *úložiště\** účet úložiště. Vyhledejte například **storagedop4k\*** .
1. Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu **účtu úložiště** .
1. V podokně **\*úložiště** vyberte **otevřít v průzkumníkovi** a otevřete Průzkumník služby Azure Storage aplikaci.
1. V levém podokně vyberte **kontejnery objektů BLOB**a pak vyberte **protokoly úloh**.
1. V podokně **úlohy – protokoly** vyberte **Stáhnout**.
1. Stáhněte protokoly do místní složky na vašem počítači.
1. Odešlete stažený soubor. zip do farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Shromažďovat protokoly služby App Service DataHub

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Do **vyhledávacího** pole vyhledejte skupinu prostředků FarmBeats DataHub.

    > [!NOTE]
    > Vyberte skupinu prostředků DataHub, kterou jste zadali během nasazování FarmBeats.

1. Ve skupině prostředků vyhledejte *datahublogs\** účet úložiště. Vyhledejte například **fordatahublogsmvxmq\*** .
1. Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu **účtu úložiště** .
1. V podokně **\*datahubblogs** vyberte **otevřít v průzkumníkovi** a otevřete Průzkumník služby Azure Storage aplikace.
1. V levém podokně vyberte **kontejnery objektů BLOB**a pak vyberte **appinsights-logs**.
1. V podokně **appinsights-logs** vyberte **Stáhnout**.
1. Stáhněte protokoly do místní složky na vašem počítači.
1. Odešlete stažený soubor. zip do farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Shromáždit protokoly služby akcelerátor App Service

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Do **vyhledávacího** pole vyhledejte skupinu prostředků akcelerátoru FarmBeats.

    > [!NOTE]
    > Vyberte skupinu prostředků akcelerátoru FarmBeats, která se poskytla během nasazování FarmBeats.

1. Ve skupině prostředků vyhledejte *\*úložiště* účtu úložiště. Vyhledejte například **storagedop4k\*** .
1. Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu **účtu úložiště** .
1. V podokně **\*úložiště** vyberte **otevřít v průzkumníkovi** a otevřete Průzkumník služby Azure Storage aplikaci.
1. V levém podokně vyberte **kontejnery objektů BLOB**a pak vyberte **appinsights-logs**.
1. V podokně **appinsights-logs** vyberte **Stáhnout**.
1. Stáhněte protokoly do místní složky na vašem počítači.
1. Odešle staženou složku do farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Známé problémy

## <a name="batch-related-issues"></a>Problémy související se službou Batch

**Chybová zpráva**: dosáhla se kvóta pro místní účet účtů Batch pro zadané předplatné.

**Nápravná opatření**: Zvyšte kvótu nebo odstraňte nepoužívané účty Batch a spusťte nasazení znovu.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problémy související s Azure Active Directory (Azure AD)

**Chybová zpráva**: "nepovedlo se aktualizovat požadovaná nastavení na aplikace Azure AD d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: nedostatečná oprávnění k dokončení operace. Zajistěte, aby byla výše nakonfigurovaná nastavení pro Aplikace Azure AD správná. "

**Význam**: Konfigurace registrace aplikace Azure AD se nedokončila správně.  

**Nápravná opatření**: požádejte správce IT (osoba s oprávněním pro čtení), aby používala náš [skript](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) pro vytvoření registrace aplikace Azure AD. Tento skript automaticky postará o kroky konfigurace i.

**Chybová zpráva**: v tomto tenantovi se nepovedlo vytvořit novou aplikaci Active Directory\<název aplikace\>v tomto tenantovi: už existuje jiný objekt se stejnou hodnotou identifikátorů URI identifikátorů vlastností.

**Význam**: registrace aplikace služby Azure AD se stejným názvem již existuje.

**Nápravná opatření**: odstraňte existující registraci aplikace Azure AD nebo ji znovu použijte k instalaci. Pokud znovu používáte stávající registraci aplikace služby Azure AD, předejte ID aplikace a tajný kód klienta instalačnímu programu a znovu nasaďte.

## <a name="issues-with-the-inputjson-file"></a>Problémy se souborem Input. JSON

**Chyba**: při čtení vstupu ze souboru *input. JSON* došlo k chybě.

**Nápravná opatření**: k tomuto problému obvykle dochází z důvodu chyby při zadání správné cesty k souboru *input. JSON* nebo názvu instalačnímu programu. Proveďte příslušné opravy a opakujte opětovné nasazení.

**Chyba**: došlo k chybě při analýze hodnot ve *vstupním souboru. JSON* .

**Nápravná opatření**: k tomuto problému většinou dochází v důsledku nesprávných hodnot v souboru *input. JSON* . Proveďte příslušné opravy a opakujte nasazení.

## <a name="high-cpu-usage"></a>Vysoké využití procesoru

**Chyba**: obdržíte e-mailové upozornění, které odkazuje na *vysokou výstrahu o využití procesoru*. 

**Nápravná opatření**: 
1. Přejít do skupiny prostředků FarmBeats DataHub.
1. Vyberte službu App Service.  
1. Přejít na [stránku s cenami](https://azure.microsoft.com/pricing/details/app-service/windows/)horizontálního navýšení kapacity App Service a pak vyberte příslušnou cenovou úroveň.

## <a name="next-steps"></a>Další kroky

Pokud pořád FarmBeats problémy, obraťte se na [fórum podpory](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
