---
title: Řešení potíží s FarmBeats
description: Tento článek popisuje, jak řešit potíže Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113495"
---
# <a name="troubleshoot"></a>Řešení potíží

Tento článek obsahuje řešení běžných problémů Azure FarmBeats. Pro další pomoc kontaktujte naše [fórum podpory](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) nebo nám napište na adresu farmbeatssupport@microsoft.com.

> [!NOTE]
  > Pokud jste nainstalovali FarmBeats během dubna a vaše úlohy selhávají s prázdnou chybovou zprávou, vaše instalace pravděpodobně nebyla přidělena žádná dávková kvóta, aby byla upřednostněna podpora pro kritické organizace pro bezpečnost a ochranu zdraví. Více informací naleznete [zde.](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) Budete muset požádat virtuální chody, které mají být přiděleny k účtu Batch úspěšně spustit úlohy.

## <a name="install-issues"></a>Problémy s instalací

  > [!NOTE]
  > Pokud restartujete instalaci z důvodu chyby, před opětovným spuštěním instalace odstraňte **skupinu prostředků** nebo odstraňte všechny prostředky ze skupiny prostředků.

### <a name="invalid-sentinel-credentials"></a>Neplatná pověření sentinelu

Pověření Sentinel uzadaných během instalace jsou nesprávné. Restartujte instalaci se správnými pověřeními.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Kvóta regionálního účtu dávkových účtů pro zadané předplatné byla dosažena.

Zvyšte kvótu nebo odstraňte nepoužívané dávkové účty a restartujte instalaci.

### <a name="invalid-resource-group-location"></a>Neplatné umístění skupiny prostředků

Ujistěte se, **že skupina prostředků** je ve stejném umístění jako **oblast** zadaná během instalace.

### <a name="other-install-issues"></a>Další problémy s instalací

Kontaktujte nás s následujícími údaji:

- ID předplatného
- Název skupiny prostředků
- Postupujte podle následujících kroků a připojte soubor protokolu pro selhání nasazení:

    1. Přejděte do **skupiny prostředků** na webu Azure Portal.

    2. V části **Nastavení** na levé straně vyberte **Nasazení.**

    3. Pro každé nasazení, které zobrazuje **neúspěšné**, vyberte až do podrobností a stáhněte podrobnosti o nasazení. Připojte tento soubor k e-mailu.

## <a name="sensor-telemetry"></a>Telemetrie senzoru

### <a name="cant-view-telemetry-data"></a>Nelze zobrazit telemetrická data

**Příznak**: Zařízení nebo senzory jsou nasazeny a jste propojili FarmBeats s partnerem zařízení, ale nemůžete získat nebo zobrazit telemetrická data na FarmBeats.

**Nápravná opatření**

1. Přejděte do skupiny prostředků FarmBeats Datahub.
2. Vyberte **centrum událostí** (DatafeedEventHubNamespace) a zkontrolujte počet příchozích zpráv.
3. Proveďte jednu z následujících akcí:

   - Pokud nejsou *k dispozici žádné příchozí zprávy*, obraťte se na partnera zařízení.  
   - Pokud se jedná *o příchozí zprávy*, kontaktujte nás pomocí protokolů Datahub a Accelerator a zachycené telemetrie.

Chcete-li pochopit, jak stahovat protokoly, přejděte do části [Shromáždit protokoly ručně.](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nelze zobrazit telemetrická data po požití historických/streamovaných dat ze senzorů

**Příznak**: Zařízení nebo senzory jsou nasazeny a vytvořili jste zařízení / senzory na FarmBeats a pozůstalé telemetrii na EventHub, ale nemůžete získat nebo zobrazit telemetrická data na FarmBeats.

**Nápravná opatření**

1. Ujistěte se, že jste provedli registraci partnera správně - můžete to zkontrolovat tak, že přejdete na vaše datahub swagger, přejděte na /Partner API, Do get a zkontrolujte, zda je partner registrován. Pokud ne, přidejte partnera následujícím [postupem.](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

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

**Nápravná opatření**

1. V Datahub Swagger přejděte do rozhraní API pro partnery.
2. Vyberte **Možnost Získat** > **Vyzkoušet** > **spustit**.

> [!NOTE]
> ID partnera senzorového partnera, který vás zajímá.

3. Vraťte se do rozhraní API partnera a vyberte **získat/\<id>**.
4. Zadejte ID partnera z kroku 3 a pak vyberte **Spustit**.

   Odpověď rozhraní API by měla mít připojovací řetězec Centra událostí.

### <a name="device-appears-offline"></a>Zařízení se zobrazí offline

**Příznaky**: Zařízení jsou nainstalována a propojili jste farmbeaty s partnerem vašeho zařízení. Zařízení jsou online a odesílají telemetrická data, ale zobrazují se offline.

**Nápravná opatření** Interval vytváření sestav není pro toto zařízení nakonfigurován. Chcete-li nastavit interval vytváření sestav, obraťte se na výrobce zařízení. 

### <a name="error-deleting-a-device"></a>Při odstraňování zařízení došlo k chybě.

Při odstraňování zařízení se může vyskytnat jeden z následujících běžných chybových scénářů:  

**Zpráva**: "Zařízení je v senzorech odkazováno: K zařízení je přidružen jeden nebo více senzorů. Odstraňte senzory a poté smažte zařízení."  

**Význam:** Zařízení je spojeno s více senzory, které jsou nasazeny ve farmě.

**Nápravná opatření**  

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
    > Partneři nemají oprávnění k odstranění zařízení nebo senzoru. Oprávnění k odstranění mají pouze správci.

## <a name="issues-with-jobs"></a>Problémy s pracovními místy

### <a name="farmbeats-internal-error"></a>Vnitřní chyba FarmBeats

**Zpráva**: "FarmBeats vnitřní chyba, naleznete v průvodci odstraňováním potíží pro více informací."

**Nápravná opatření** Tento problém může vyplývat z dočasné selhání v datovém kanálu. Vytvořte úlohu znovu. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

## <a name="accelerator-troubleshooting"></a>Poradce při potížích s akcelerátorem

### <a name="access-control"></a>Řízení přístupu

**Problém**: Při přidávání přiřazení role se zobrazí chyba.

**Zpráva**: "Nebyl nalezen žádný odpovídající uživatel."

**Nápravná opatření** Zkontrolujte ID e-mailu, pro který se pokoušíte přidat přiřazení role. ID e-mailu musí být přesná shoda ID, které je pro tohoto uživatele registrováno ve službě Active Directory. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

### <a name="unable-to-log-in-to-accelerator"></a>Nelze se přihlásit k akcelerátoru.

**Zpráva**: "Chyba: Nemáte oprávnění k volání služby. Obraťte se na správce pro autorizaci."

**Nápravná opatření** Požádejte správce, aby vám povolil přístup k nasazení FarmBeats. To lze provést provedením post roleassignment API nebo prostřednictvím řízení přístupu v podokně **Nastavení** v akcelerátoru.  

Pokud vám již byl udělen přístup a čelíte této chybě, zkuste to znovu aktualizací stránky. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problémy s akcelerátorem  

**Problém**: Došlo k chybě akcelerátoru z neurčené příčiny.

**Zpráva**: "Chyba: Došlo k neznámé chybě."

**Nápravná opatření** K této chybě dochází, pokud necháte stránku nečinnosti příliš dlouho. Aktualizujte stránku. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

**Problém**: FarmBeats Accelerator nezobrazuje nejnovější verzi, a to i poté, co jste upgradovali FarmBeatsDeployment.

**Nápravná opatření** K této chybě dochází z důvodu trvalost pracovníka služby v prohlížeči. Udělejte toto:

1. Zavřete všechny karty prohlížeče s otevřeným akcelerátorem a zavřete okno prohlížeče.
2. Spusťte novou instanci prohlížeče a znovu načtěte identifikátor URI akcelerátoru. Tato akce načte novou verzi akcelerátoru.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problémy související se snímky

### <a name="wrong-username-or-password"></a>Nesprávné uživatelské jméno nebo heslo

**Zpráva o selhání úlohy**: "Pro přístup k tomuto prostředku je vyžadováno úplné ověření."

**Nápravná opatření**: Proveďte jednu z následujících akcí:

- Aktualizujte FarmBeats se správným uživatelským jménem / heslem pomocí níže uvedených kroků a opakujte úlohu.

  **Aktualizovat uživatelské jméno Sentinelu**

    1. Přihlaste se k [portálu Azure](https://portal.azure.com).
    2. V poli **Hledat** vyhledejte skupinu prostředků FarmBeats Datahub.
    3. Vyberte úložiště účtu úložiště***** > **kontejnery** > **dávkové přípravné soubory** > **to_vm** > **config.ini**
    4. Vybrat **Upravit**
    5. Aktualizace uživatelského jména v části sentinel_account

  **Aktualizovat heslo sentinelu**

    1. Přihlaste se k [portálu Azure](https://portal.azure.com).
    2. V poli **Hledat** vyhledejte skupinu prostředků FarmBeats Datahub.
    3. Vyberte trezor kláves-*****
    4. V části Nastavení vyberte zásady přístupu.
    5. Vybrat **přidat zásady přístupu**
    6. Použití **správy tajných kódů** pro konfiguraci ze šablony a přidání do hlavního serveru
    7. Vyberte **Přidat**a pak na stránce **Zásady přístupu** vyberte **Uložit.**
    8. Vybrat **tajné kódy** v části **Nastavení**
    9. Vybrat **sentinel-heslo**
    10. Vytvořte novou verzi hodnoty a povolte ji.

- Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a zkontrolujte, zda je úloha úspěšná.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centrum Sentinel: Nesprávná adresa URL nebo web není přístupný

**Zpráva o selhání práce**: "Jejda, něco se pokazilo. Stránka, ke které jste se pokoušeli získat přístup, je (dočasně) nedostupná."

**Nápravná opatření**:

1. Otevřete [sentinel](https://scihub.copernicus.eu/dhus/) ve svém prohlížeči, abyste zjistili, zda je web přístupný.
2. Pokud web není přístupný, zkontrolujte, zda přístup k webu nebrání nějaký bránový firewall, firemní síť nebo jiný blokovací software, a pak podnikněte nezbytné kroky k povolení adresy URL sentinelu. 
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

**Význam:** Pokud úloha selže, protože bylo dosaženo maximálního počtu připojení, stejný účet Sentinel se používá ve více úlohách.

**Nápravná opatření**: Vyzkoušejte některou z následujících možností:

* Před opětovným spuštěním neúspěšné úlohy počkejte na dokončení ostatních úloh.
* Vytvořte si nový účet Sentinel a pak aktualizujte uživatelské jméno sentinelu a heslo v FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Server Sentinel: Odmítnuté připojení

**Zpráva o selhání úlohy**: http://172.30.175.69:8983/solr/dhus"Server odmítl připojení na adrese: ."

**Nápravná opatření**: K tomuto problému může dojít, pokud jsou na serveru Sentinel prováděny všechny aktivity údržby.

1. Pokud se některá úloha nebo kanál nezdaří, protože se provádí údržba, znovu odešlete úlohu po určité době.

   Informace o plánovaných nebo neplánovaných aktivitách údržby sentinelu najdete na webu [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a zkontrolujte, zda je úloha úspěšná.

### <a name="soil-moisture-map-has-white-areas"></a>Půdní vlhkost mapa má bílé plochy

**Problém**: **Mapa Půdní vlhkost** byla vygenerována, ale mapa má většinou bílé plochy.

**Nápravná opatření**: K tomuto problému může dojít, pokud satelitní indexy generované pro čas, pro který byla požadována mapa má ndvi hodnoty, které je menší než 0,3. Další informace naleznete na [stránce Technická příručka od společnosti Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Znovu spusťte úlohu pro jiné časové období a zkontrolujte, zda jsou hodnoty NDVI v satelitních indexech větší než 0,3.

## <a name="collect-logs-manually"></a>Ruční sběr protokolů

[Nainstalujte a nasaďte Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Shromažďování protokolů úloh Azure Data Factory nebo protokolů služby App Service v Datahubu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V poli **Hledat** vyhledejte skupinu prostředků FarmBeats Datahub.
3. Na řídicím panelu **Skupiny prostředků** vyhledejte účet úložiště *\* datahublogs.* Například *datahublogsmvxmq*.  
4. Ve sloupci **Název** vyberte účet úložiště, který chcete zobrazit řídicí panel **Účtu úložiště.**
5. V podokně **datahubblogs\* ** vyberte **Otevřít v Průzkumníkovi** a zobrazte aplikaci Open Azure Storage **Explorer.**
6. V levém podokně vyberte **kontejnery objektů blob**a pak vyberte **protokoly úloh** pro protokoly Azure Data Factory nebo **protokoly appinsights** pro protokoly služby App Service.
7. Vyberte **Stáhnout** a stáhněte protokoly do místní složky v počítači.

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Shromažďování protokolů úloh Azure Data Factory nebo protokolů služby App Service pro akcelerátor

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V poli **Hledat** vyhledejte skupinu zdrojů Akcelerátoru FarmBeats.
3. Na řídicím panelu **Skupiny prostředků** *vyhledejte\* * účet úložiště. Například *storagedop4k\**.
4. Vyberte účet úložiště ve sloupci **Název** a zobrazte řídicí panel **Účtu úložiště.**
5. V podokně **úložiště\* ** vyberte **Otevřít v Průzkumníkovi** a otevřete aplikaci Průzkumníka úložiště Azure.
6. V levém podokně vyberte **kontejnery objektů blob**a pak vyberte **protokoly úloh** pro protokoly Azure Data Factory nebo **protokoly appinsights** pro protokoly služby App Service.
7. Vyberte **Stáhnout** a stáhněte protokoly do místní složky v počítači.

## <a name="high-cpu-usage"></a>Vysoké využití procesoru

**Chyba**: Zobrazí se e-mailové upozornění, které odkazuje na **upozornění na vysoké využití procesoru**.

**Nápravná opatření**:

1. Přejděte do skupiny prostředků FarmBeats Datahub.
2. Vyberte **službu App .**  
3. Přejděte na [stránku s cenami služby App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)a vyberte příslušnou cenovou úroveň.
