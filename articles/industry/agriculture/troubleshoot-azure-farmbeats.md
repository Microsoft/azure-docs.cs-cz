---
title: Řešení potíží s FarmBeats
description: Tento článek popisuje, jak řešit potíže Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422724"
---
# <a name="troubleshoot"></a>Řešení potíží

Tento článek obsahuje řešení běžných problémů Azure FarmBeats.

Pro další pomoc nás farmbeatssupport@microsoft.comkontaktujte na adrese . Ujistěte se, že do e-mailu zahrnete soubor **deployer.log.**

Chcete-li stáhnout soubor **deployer.log,** postupujte takto:

1. Přihlaste se k **portálu Azure portal** a vyberte předplatné a tenanta Azure AD.
2. Spusťte Cloud Shell z horního navigačního panelu na webu Azure Portal.
3. Jako preferované prostředí Cloud Shell vyberte **Bash.**
4. Vyberte zvýrazněnou ikonu a pak v rozevíracím seznamu vyberte **Stáhnout**.

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. V dalším podokně zadejte cestu k souboru **deployer.log.** Zadejte například **farmbeats-deployer.log**.

## <a name="sensor-telemetry"></a>Telemetrie senzoru

### <a name="cant-view-telemetry-data"></a>Nelze zobrazit telemetrická data

**Příznak**: Zařízení nebo senzory jsou nasazeny a jste propojili FarmBeats s partnerem zařízení, ale nemůžete získat nebo zobrazit telemetrická data na FarmBeats.

**Nápravná opatření**:

1. Přejděte do skupiny prostředků FarmBeats Datahub.   

2. Vyberte **centrum událostí** (DatafeedEventHubNamespace) a zkontrolujte počet příchozích zpráv.

3. Proveďte jednu z následujících akcí:   
   - Pokud nejsou *k dispozici žádné příchozí zprávy*, obraťte se na partnera zařízení.  
   - Pokud se jedná o farmbeatssupport@microsoft.compříchozí *zprávy*, kontaktujte . Připojte protokoly Datahub a Akcelerátor a zachycenou telemetrii.

Chcete-li pochopit, jak stahovat protokoly, přejděte do části [Shromáždit protokoly ručně.](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nelze zobrazit telemetrická data po požití historických/streamovaných dat ze senzorů

**Příznak**: Zařízení nebo senzory jsou nasazeny a vytvořili jste zařízení / senzory na FarmBeats a pozůstalé telemetrii na EventHub, ale nemůžete získat nebo zobrazit telemetrická data na FarmBeats.

**Nápravná opatření**:

1. Ujistěte se, že jste provedli registraci partnera správně - můžete to zkontrolovat tak, že přejdete na vaše datahub swagger, přejděte na /Partner API, Do get a zkontrolujte, zda je partner registrován. Pokud ne, postupujte [podle pokynů zde](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) přidat partnera.

2. Ujistěte se, že jste použili správný formát zprávy telemetrie:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nemáte připojovací řetězec Azure Event Hubs

**Nápravná opatření**:

1. V Datahub Swagger přejděte do rozhraní API pro partnery.
2. Vyberte **Možnost Získat** > **Vyzkoušet** > **spustit**.

> [!NOTE]
> ID partnera senzorového partnera, který vás zajímá.

3. Vraťte se do rozhraní API partnera a vyberte **získat/\<id>**.
4. Zadejte ID partnera z kroku 3 a pak vyberte **Spustit**.

   Odpověď rozhraní API by měla mít připojovací řetězec Centra událostí.

### <a name="device-appears-offline"></a>Zařízení se zobrazí offline

**Příznaky**: Zařízení jsou nainstalována a propojili jste farmbeaty s partnerem vašeho zařízení. Zařízení jsou online a odesílají telemetrická data, ale zobrazují se offline.

**Nápravná akce**: Interval vykazování není pro toto zařízení nakonfigurován. Chcete-li nastavit interval vytváření sestav, obraťte se na výrobce zařízení. 

### <a name="error-deleting-a-device"></a>Při odstraňování zařízení došlo k chybě.

Při odstraňování zařízení se může vyskytnat jeden z následujících běžných chybových scénářů:  

**Zpráva**: "Zařízení je v senzorech odkazováno: K zařízení je přidružen jeden nebo více senzorů. Odstraňte senzory a poté smažte zařízení."  

**Význam:** Zařízení je spojeno s více senzory, které jsou nasazeny ve farmě.   

**Nápravná opatření**:  

1. Odstraňte senzory, které jsou přidruženy k zařízení prostřednictvím akcelerátoru.  
2. Chcete-li senzory přidružit k jinému zařízení, požádejte partnera zařízení, aby učinil totéž.  
3. Odstraňte zařízení pomocí `DELETE API` volání a nastavte parametr force jako *true*.  

**Zpráva**: "Zařízení je odkazováno v zařízeních jako ParentDeviceId: Existuje jedno nebo více zařízení, které jsou přidruženy k tomuto zařízení jako podřízená zařízení. Odstraňte je a pak odstraňte toto zařízení."  

**Význam:** Vaše zařízení má další zařízení s ním spojené.  

**Nápravná opatření**

1. Odstraňte zařízení, která jsou přidružena k tomuto konkrétnímu zařízení.  
2. Odstraňte konkrétní zařízení.  

    > [!NOTE]
    > Zařízení nelze odstranit, pokud jsou k němu přidruženy senzory. Další informace o odstranění přidružených senzorů naleznete v části **Odstranit senzor** v části [Získání dat senzoru od partnerů senzorů](get-sensor-data-from-sensor-partner.md).

    > Partneři nemají přístup k odstranění zařízení nebo senzoru. Pouze správci mají přístup k tomu, aby učinili totéž.


## <a name="issues-with-jobs"></a>Problémy s pracovními místy

### <a name="farmbeats-internal-error"></a>Vnitřní chyba FarmBeats

**Zpráva**: "Vnitřní chyba FarmBeats, další podrobnosti naleznete v průvodci odstraňováním potíží."

**Nápravná opatření**: Tento problém může vyplývat z dočasné selhání v datovém kanálu. Vytvořte úlohu znovu. Pokud chyba přetrvává, přidejte chybovou zprávu v příspěvku na FarmBeatsSupport@microsoft.comfóru FarmBeats nebo kontakt .

## <a name="accelerator-troubleshooting"></a>Poradce při potížích s akcelerátorem

### <a name="access-control"></a>Řízení přístupu

**Problém**: Při přidávání přiřazení role se zobrazí chyba.

**Zpráva**: "Nebyl nalezen žádný odpovídající uživatel."

**Nápravná akce**: Zkontrolujte ID e-mailu, pro který se pokoušíte přidat přiřazení role. ID e-mailu musí být přesná shoda ID, které je pro tohoto uživatele registrováno ve službě Active Directory. Pokud chyba přetrvává, přidejte chybovou zprávu v příspěvku na FarmBeatsSupport@microsoft.comfóru FarmBeats nebo kontakt .

### <a name="unable-to-log-in-to-accelerator"></a>Nelze se přihlásit k akcelerátoru.

**Zpráva**: "Chyba: Nemáte oprávnění k volání služby. Obraťte se na správce pro autorizaci."

**Nápravná opatření**: Požádejte správce, aby vás autorizoval k přístupu k nasazení FarmBeats. To lze provést provedením post roleassignment API nebo prostřednictvím řízení přístupu v podokně **Nastavení** v akcelerátoru.  

Pokud vám již byl udělen přístup a čelíte této chybě, zkuste to znovu aktualizací stránky. Pokud chyba přetrvává, přidejte chybovou zprávu v příspěvku na FarmBeatsSupport@microsoft.comfóru FarmBeats nebo kontakt .

![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problémy s akcelerátorem  

**Problém**: Došlo k chybě akcelerátoru z neurčené příčiny.

**Zpráva**: "Chyba: Došlo k neznámé chybě."

**Nápravná akce**: K této chybě dochází, pokud ponecháte stránku nečinnou příliš dlouho. Aktualizujte stránku.  

Pokud chyba přetrvává, přidejte chybovou zprávu v příspěvku na FarmBeatsSupport@microsoft.comfóru FarmBeats nebo kontakt .

**Problém**: FarmBeats Accelerator nezobrazuje nejnovější verzi, a to i poté, co jste upgradovali FarmBeatsDeployment.

**Nápravná akce**: K této chybě dochází z důvodu trvalosti servisního pracovníka v prohlížeči. Udělejte toto:

1. Zavřete všechny karty prohlížeče s otevřeným akcelerátorem a zavřete okno prohlížeče.
2. Spusťte novou instanci prohlížeče a znovu načtěte identifikátor URI akcelerátoru. Tato akce načte novou verzi akcelerátoru.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problémy související se snímky

### <a name="wrong-username-or-password"></a>Nesprávné uživatelské jméno nebo heslo

**Zpráva o selhání úlohy**: "Pro přístup k tomuto prostředku je vyžadováno úplné ověření."

**Nápravná opatření**:

Proveďte jednu z těchto akcí:

- Znovu spusťte instalační program pro upgrade Datahub se správným uživatelským jménem a heslem.
- Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a zkontrolujte, zda je úloha úspěšná.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centrum Sentinel: Nesprávná adresa URL nebo web není přístupný 

**Zpráva o selhání práce**: "Jejda, něco se pokazilo. Stránka, ke které jste se pokoušeli získat přístup, je (dočasně) nedostupná." 

**Nápravná opatření**:
1. Otevřete [sentinel](https://scihub.copernicus.eu/dhus/) ve svém prohlížeči, abyste zjistili, zda je web přístupný. 
2. Pokud web není přístupný, zkontrolujte, zda přístup k webu nebrání nějaký bránový firewall, firemní síť nebo jiný blokovací software, a proveďte nezbytné kroky k povolení adresy URL sentinelu. 
3. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a zkontrolujte, zda je úloha úspěšná.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel server: Dolů pro údržbu

**Zpráva o selhání úlohy**: "Centrum s otevřeným přístupem Copernicus se brzy vrátí! Omlouváme se za nepříjemnosti, provádíme nějakou údržbu v tuto chvíli. Brzy se vrátíme na internet!" 

**Nápravná opatření**:

K tomuto problému může dojít, pokud jsou na serveru Sentinel prováděny všechny aktivity údržby.

1. Pokud se některá úloha nebo kanál nezdaří, protože se provádí údržba, znovu odešlete úlohu po určité době. 

   Informace o plánovaných nebo neplánovaných aktivitách údržby sentinelu najdete na webu [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a zkontrolujte, zda je úloha úspěšná.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Maximální počet připojení dosažených

**Zpráva o selhání úlohy**: "Maximální počet dvou\<souběžných toků dosažených uživatelským jménem>"."

**Význam:** Pokud úloha selže, protože bylo dosaženo maximálního počtu připojení, stejný účet Sentinel se používá v jiném nasazení softwaru.

**Nápravná opatření**: Vyzkoušejte některou z následujících možností:

* Vytvořte nový účet Sentinel a potom znovu spusťte instalační program pro upgrade Datahubu pomocí nového uživatelského jména a hesla Sentinelu.  
* Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a zkontrolujte, zda je úloha úspěšná.

### <a name="sentinel-server-refused-connection"></a>Server Sentinel: Odmítnuté připojení 

**Zpráva o selhání úlohy**: http://172.30.175.69:8983/solr/dhus"Server odmítl připojení na adrese: ." 

**Nápravná opatření**: K tomuto problému může dojít, pokud jsou na serveru Sentinel prováděny všechny aktivity údržby. 
1. Pokud se některá úloha nebo kanál nezdaří, protože se provádí údržba, znovu odešlete úlohu po určité době. 

   Informace o plánovaných nebo neplánovaných aktivitách údržby sentinelu najdete na webu [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a zkontrolujte, zda je úloha úspěšná.

### <a name="soil-moisture-map-has-white-areas"></a>Půdní vlhkost mapa má bílé plochy 

**Problém**: Mapa Půdní vlhkost byla vygenerována, ale mapa má většinou bílé plochy.

**Nápravná opatření**: K tomuto problému může dojít, pokud satelitní indexy generované pro čas, pro který byla požadována mapa má ndvi hodnoty, které je menší než 0,3. Další informace naleznete na [stránce Technického průvodce od společnosti Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).
1. Znovu spusťte úlohu pro jiné časové období a zkontrolujte, zda hodnoty NDVI v satelitních indexech jsou větší než 0,3

## <a name="collect-logs-manually"></a>Ruční sběr protokolů

[Nainstalujte a nasaďte Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Shromažďování protokolů úloh Azure Data Factory v Datahubu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V poli **Hledat** vyhledejte skupinu prostředků FarmBeats Datahub.

    > [!NOTE]
    > Vyberte skupinu prostředků Datahub, kterou jste zadali během instalace FarmBeats.

3. Na řídicím panelu **Skupiny prostředků** vyhledejte účet úložiště *\* datahublogs.* Například vyhledejte **datahublogsmvxmq**.  
4. Ve sloupci **Název** vyberte účet úložiště, který chcete zobrazit řídicí panel **Účtu úložiště.**
5. V podokně **datahubblogs\* ** vyberte **Otevřít v Průzkumníkovi** a zobrazte aplikaci Open Azure Storage **Explorer.**
6. V levém podokně vyberte **kontejnery objektů blob**a pak vyberte **protokoly úloh**.
7. V podokně **protokolů úloh** vyberte **stáhnout**.
8. Stáhněte protokoly do místní složky v počítači.
9. Odeslat stažený soubor farmbeatssupport@microsoft.comZIP e-mailem na adresu .

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Shromažďování protokolů úloh Azure Data Factory v akcelerátoru

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V poli **Hledat** vyhledejte skupinu zdrojů Akcelerátoru FarmBeats.

    > [!NOTE]
    > Vyberte skupinu prostředků akcelerátoru, kterou jste zadali během instalace FarmBeats.

3. Na řídicím panelu **Skupiny prostředků** *vyhledejte\* * účet úložiště. Například vyhledejte **storagedop4k\***.
4. Vyberte účet úložiště ve sloupci **Název** a zobrazte řídicí panel **Účtu úložiště.**
5. V podokně **úložiště\* ** vyberte **Otevřít v Průzkumníkovi** a otevřete aplikaci Průzkumníka úložiště Azure.
6. V levém podokně vyberte **kontejnery objektů blob**a pak vyberte **protokoly úloh**.
7. V podokně **protokolů úloh** vyberte **stáhnout**.
8. Stáhněte protokoly do místní složky v počítači.
9. Odeslat stažený soubor farmbeatssupport@microsoft.comZIP e-mailem na adresu .


### <a name="collect-datahub-app-service-logs"></a>Shromažďování protokolů služeb aplikace Datahub

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V poli **Hledat** vyhledejte skupinu prostředků FarmBeats Datahub.

    > [!NOTE]
    > Vyberte skupinu prostředků Datahub, kterou jste zadali během instalace FarmBeats.

3. Ve skupině prostředků vyhledejte účet úložiště *datahublogs.\* * Například vyhledejte **prodatahublogsmvxmq\***.
4. Vyberte účet úložiště ve sloupci **Název** a zobrazte řídicí panel **Účtu úložiště.**
5. V podokně **datahubblogs\* ** vyberte **Otevřít v Průzkumníkovi** a otevřete aplikaci Azure Storage Explorer.
6. V levém podokně vyberte **Kontejnery objektů blob**a pak vyberte **protokoly appinsights .**
7. V podokně **protokolů appinsights** vyberte **Stáhnout**.
8. Stáhněte protokoly do místní složky v počítači.
9. Odeslat stažený soubor farmbeatssupport@microsoft.comZIP e-mailem na adresu .

### <a name="collect-accelerator-app-service-logs"></a>Shromažďovat protokoly služby aplikace akcelerátoru

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V poli **Hledat** vyhledejte skupinu zdrojů Akcelerátoru FarmBeats.

    > [!NOTE]
    > Vyberte skupinu zdrojů FarmBeats Accelerator, která byla poskytnuta během instalace FarmBeats.

3. Ve skupině prostředků *vyhledejte\* * účet úložiště. Například vyhledejte **storagedop4k\***.
4. Vyberte účet úložiště ve sloupci **Název** a zobrazte řídicí panel **Účtu úložiště.**
5. V podokně **úložiště\* ** vyberte **Otevřít v Průzkumníkovi** a otevřete aplikaci Průzkumníka úložiště Azure.
6. V levém podokně vyberte **Kontejnery objektů blob**a pak vyberte **protokoly appinsights .**
7. V podokně **protokolů appinsights** vyberte **Stáhnout**.
8. Stáhněte protokoly do místní složky v počítači.
9. Pošlete stažené farmbeatssupport@microsoft.comsložce e-mail na adresu .

## <a name="known-issues"></a>Známé problémy

## <a name="batch-related-issues"></a>Problémy související s dávkou

**Chybová zpráva**: "Byla dosažena kvóta regionálního účtu dávkových účtů pro zadané předplatné."

**Nápravná opatření**: Zvyšte kvótu nebo odstraňte nepoužívané dávkové účty a znovu spusťte nasazení.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problémy související s Azure Active Directory (Azure AD)

**Chybová zpráva**: "Nelze aktualizovat požadované nastavení aplikace Azure AD D41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: Nedostatečná oprávnění k dokončení operace. Ujistěte se, že výše uvedená nastavení jsou správně nakonfigurovaná pro aplikaci Azure AD."

**Význam:** Konfigurace registrace aplikace Azure AD nebyla správně dokončena.  

**Nápravná opatření**: Požádejte správce IT (osobu s přístupem klienta pro čtení) o použití našeho [skriptu](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) pro vytvoření registrace aplikace Azure AD. Tento skript se automaticky postará také o kroky konfigurace.

**Chybová zpráva**: V tomto tenantovi\>nelze vytvořit nový název\<aplikace služby Active Directory : Jiný objekt se stejnou hodnotou identifikátoru IDENTIFIKÁTOR URIs již existuje.

**Význam**: Registrace aplikace Azure AD se stejným názvem již existuje.

**Nápravná opatření**: Odstraňte existující registraci aplikace Azure AD nebo ji znovu použijte pro instalaci. Pokud znovu používáte existující registraci aplikace Azure AD, předaj id aplikace a tajný klíč klienta instalačnímu programu a znovu nasadit.

## <a name="issues-with-the-inputjson-file"></a>Problémy se souborem input.json

**Chyba**: Došlo k chybě při čtení vstupu ze souboru *input.json.*

**Nápravná opatření**: Tento problém obvykle vzniká z důvodu chyby při zadávání správné cesty nebo názvu souboru *input.json* instalačnímu programu. Proveďte příslušné opravy a opakujte nasazení.

**Chyba**: V souboru *input.json* došlo k chybě.

**Nápravná opatření**: Tento problém vzniká většinou z důvodu nesprávné hodnoty v rámci *input.json* souboru. Proveďte příslušné opravy a opakujte nasazení.

## <a name="high-cpu-usage"></a>Vysoké využití procesoru

**Chyba**: Zobrazí se e-mailové upozornění, které odkazuje na **upozornění na vysoké využití procesoru**. 

**Nápravná opatření**: 
1. Přejděte do skupiny prostředků FarmBeats Datahub.
2. Vyberte **službu App .**  
3. Přejděte na [stránku s cenami služby App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)a vyberte příslušnou cenovou úroveň.

## <a name="next-steps"></a>Další kroky

Pokud stále čelíte problémům s FarmBeats, obraťte se na naše [fórum podpory](https://aka.ms/farmbeatssupport).
