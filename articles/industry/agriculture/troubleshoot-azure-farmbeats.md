---
title: Řešení potíží s FarmBeats
description: Tento článek popisuje, jak řešit potíže s Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: cc2eb7ecb7f0587c065aac1cfb57cfae10b732ac
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168286"
---
# <a name="troubleshoot-azure-farmbeats"></a>Řešení potíží s FarmBeats

Tento článek popisuje řešení běžných problémů s FarmBeats Azure. Pokud chcete získat další pomoc, obraťte se na náš [Q&fórum podpory](https://aka.ms/farmbeatssupport) nebo na nás pošlete e-mail na adrese farmbeatssupport@microsoft.com .

> [!NOTE]
  > Pokud jste v dubnu v dubnu nainstalovali FarmBeats a vaše úlohy selžou s prázdnou chybovou zprávou, je možné, že instalace nebyla přidělena žádná kvóta služby Batch k určení priorit podpory pro kritické organizace v oblasti zdravotního stavu a zabezpečení. Další informace najdete [tady](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/). K úspěšnému spuštění úloh budete muset požádat o virtuální počítače, které mají být přiděleny k účtu Batch.

## <a name="install-issues"></a>Problémy s instalací

  > [!NOTE]
  > Pokud restartujete instalaci z důvodu chyby, ujistěte se, že jste před opětovnou aktivací instalace odstranili **skupinu prostředků** , nebo odstraňte všechny prostředky ze skupiny prostředků.

### <a name="invalid-sentinel-credentials"></a>Neplatné přihlašovací údaje ověřovacího pověření

Přihlašovací údaje Sentinel zadané při instalaci jsou nesprávné. Restartujte instalaci se správnými přihlašovacími údaji.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Pro zadané předplatné se dosáhla kvóta účtu Batch pro místní účty.

Zvyšte kvótu nebo odstraňte nepoužívané účty Batch a spusťte instalaci znovu.

### <a name="invalid-resource-group-location"></a>Neplatné umístění skupiny prostředků

Ujistěte se, že **Skupina prostředků** je ve stejném umístění jako **oblast** zadaná při instalaci.

### <a name="other-install-issues"></a>Další problémy s instalací

Kontaktujte nás s následujícími podrobnostmi:

- ID vašeho předplatného
- Název skupiny prostředků
- K připojení souboru protokolu k selhání nasazení použijte následující postup:

    1. Přejděte do **skupiny prostředků** v Azure Portal.

    2. V části **Nastavení** na levé straně vyberte **nasazení** .

    3. U každého nasazení, které **se zobrazí jako neúspěšné**, vyberte podrobnosti a Stáhněte si podrobnosti o nasazení. Umožňuje připojit tento soubor k e-mailu.

## <a name="sensor-telemetry"></a>Telemetrie senzorů

### <a name="cant-view-telemetry-data"></a>Nejde zobrazit data telemetrie.

**Příznak**: zařízení nebo senzory se nasazují a propojení FarmBeats s partnerem zařízení, ale data telemetrie nemůžete získat nebo zobrazit na FarmBeats.

**Nápravná opatření**

1. Přejít do skupiny prostředků FarmBeats.
2. Vyberte obor názvů **centra událostí** ("senzor-partner-eh-Namespace-XXXX"), klikněte na Event Hubs a pak zkontrolujte počet příchozích zpráv v centru událostí, který je přiřazený k partnerovi.
3. Proveďte jednu z následujících akcí:

   - Pokud neexistují *žádné příchozí zprávy*, obraťte se na partnera zařízení.  
   - Pokud jsou *příchozí zprávy*, kontaktujte nás s protokoly DataHub a akcelerátory a zachycenou telemetrie.

Pokud chcete pochopit, jak stahovat protokoly, přečtěte si část [shromáždění protokolů ručně](#collect-logs-manually) .  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Po ingestování historických/streamových dat ze senzorů nejde zobrazit data telemetrie.

**Příznak**: zařízení nebo senzory se nasazují a vytvořili jste zařízení/senzory na FarmBeats a ingestovaná telemetrie na EventHub, ale data telemetrie nemůžete získat nebo zobrazit na FarmBeats.

**Nápravná opatření**

1. Ujistěte se, že jste správně dokončili registraci partnera – můžete to zkontrolovat tak, že přejdete na DataHub Swagger, přejdete na rozhraní/partner API, načtěte a zkontrolujete, jestli je partner zaregistrovaný. Pokud ne, přidejte partnera pomocí těchto [kroků](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) .

2. Ujistěte se, že jste používali správný formát zprávy telemetrie:

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
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nemáte připojovací řetězec služby Azure Event Hubs.

**Nápravná opatření**

1. V DataHub Swagger navštivte rozhraní API partnera.
2. Vyberte **Get**  >  **Try**on  >  **Execute**.

> [!NOTE]
> ID partnera, se kterým se zajímá partnerka snímače.

3. Vraťte se do partnerského rozhraní API a vyberte **získat/ \<ID> **.
4. Zadejte ID partnera z kroku 3 a pak vyberte **provést**.

   Odpověď rozhraní API by měla mít Event Hubs připojovací řetězec.

### <a name="device-appears-offline"></a>Zařízení se zobrazuje v režimu offline.

**Příznaky**: zařízení jsou nainstalována a propojení FarmBeats s partnerem zařízení. Zařízení jsou online a odesílají data telemetrie, ale jsou v režimu offline.

**Nápravná opatření** Interval hlášení není pro toto zařízení nakonfigurovaný. Chcete-li nastavit interval vytváření sestav, obraťte se na výrobce zařízení. 

### <a name="error-deleting-a-device"></a>Chyba při odstraňování zařízení

Při odstraňování zařízení se může zobrazit jeden z následujících běžných scénářů chyb:  

**Zpráva**: na zařízení se odkazuje v senzorech: k zařízení je přidružen jeden nebo více senzorů. Odstraňte senzory a pak zařízení odstraňte. "  

**Význam**: zařízení je přidruženo k několika senzorům, které jsou nasazeny ve farmě.

**Nápravná opatření**  

1. Odstraňte senzory, které jsou přidružené k zařízení prostřednictvím akcelerátoru.  
2. Pokud chcete senzory přidružit k jinému zařízení, požádejte partnera zařízení, aby to provede.  
3. Odstraňte zařízení pomocí `DELETE API` volání a nastavte parametr Force na *hodnotu true*.  

**Zpráva**: na zařízení je odkazováno v zařízeních jako ParentDeviceId: k tomuto zařízení jsou přidružená aspoň jedno zařízení jako podřízená zařízení. Odstraňte je a pak toto zařízení odstraňte. "  

To **znamená**, že k zařízení jsou přidružená jiná zařízení.  

**Nápravná opatření**

1. Odstraňte zařízení, která jsou přidružená k tomuto konkrétnímu zařízení.  
2. Odstraňte konkrétní zařízení.  

    > [!NOTE]
    > Zařízení nemůžete odstranit, pokud jsou k němu přidružené senzory. Další informace o tom, jak odstranit přidružené senzory, najdete v části **odstranění senzoru** v tématu [získání dat ze senzorů od partnerů snímačů](get-sensor-data-from-sensor-partner.md).
    > Partneři nemají oprávnění odstranit zařízení nebo senzor. Pouze správci mají oprávnění k odstranění.

## <a name="issues-with-jobs"></a>Problémy s úlohami

### <a name="farmbeats-internal-error"></a>Vnitřní chyba FarmBeats

**Zpráva**: "vnitřní chyba FarmBeats, další informace najdete v Průvodci odstraňováním potíží."

**Nápravná opatření** Tento problém může být způsoben dočasným selháním v datovém kanálu. Vytvořte úlohu znovu. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

## <a name="accelerator-troubleshooting"></a>Řešení potíží s akcelerátory

### <a name="access-control"></a>Řízení přístupu

**Problém**: při přidávání přiřazení role se zobrazí chyba.

**Zpráva**: nebyla nalezena žádná vyhovující uživatelé.

**Nápravná opatření** Ověřte ID e-mailu, ke kterému se pokoušíte přidat přiřazení role. ID e-mailu musí být přesná shoda ID, která je zaregistrovaná pro daného uživatele ve službě Active Directory. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

### <a name="unable-to-log-in-to-accelerator"></a>Nepovedlo se přihlásit k akcelerátoru.

**Zpráva**: "Chyba: nemáte oprávnění k volání služby. Požádejte správce o autorizaci. "

**Nápravná opatření** Požádejte správce, aby vám povolil přístup k nasazení FarmBeats. To se dá udělat tak, že provedete příspěvek rozhraní RoleAssignment API nebo prostřednictvím Access Control v podokně **Nastavení** v akcelerátoru.  

Pokud jste už přístup k této chybě udělili a k této chybě došlo, zkuste to znovu tak, že aktualizujete stránku. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

![Snímek obrazovky znázorňující chybu autorizace](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problémy akcelerátoru  

**Problém**: zobrazila se chyba akcelerátoru neurčité příčiny.

**Zpráva**: "Chyba: došlo k neznámé chybě."

**Nápravná opatření** K této chybě dojde, pokud ponecháte stránku nečinné příliš dlouho. Aktualizujte stránku. Pokud chyba přetrvává, kontaktujte nás s chybovou zprávou/protokoly.

**Problém**: akcelerátor FarmBeats nezobrazuje nejnovější verzi, i když jste provedli upgrade FarmBeatsDeployment.

**Nápravná opatření** K této chybě dochází z důvodu trvalosti pracovního procesu služby v prohlížeči. Postupujte následovně:

1. Zavřete všechny karty prohlížeče, které mají otevřený akcelerátor, a zavřete okno prohlížeče.
2. Spusťte novou instanci prohlížeče a znovu načtěte identifikátor URI akcelerátoru. Tato akce načte novou verzi akcelerátoru.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: problémy související s snímkům

### <a name="wrong-username-or-password"></a>Nesprávné uživatelské jméno nebo heslo

**Zpráva o selhání úlohy**: pro přístup k tomuto prostředku je vyžadováno úplné ověření.

**Nápravná opatření**: proveďte jednu z následujících akcí:

- Pomocí následujících kroků aktualizujte FarmBeats se správným uživatelským jménem nebo heslem a zkuste úlohu zopakovat.

  **Aktualizovat uživatelské jméno Sentinel**

    1. Přihlaste se k [Azure Portal](https://portal.azure.com).
    2. Do **vyhledávacího** pole vyhledejte skupinu prostředků FarmBeats DataHub.
    3. Vyberte úložiště účtu úložiště * * * * > **kontejnery**  >  **Batch-PREP-Files**  >  **to_vm**  >  **config.ini**
    4. Vybrat **Upravit**
    5. Aktualizace uživatelského jména v části sentinel_account

  **Aktualizovat heslo Sentinel**

    1. Přihlaste se k [Azure Portal](https://portal.azure.com).
    2. Do **vyhledávacího** pole vyhledejte skupinu prostředků FarmBeats DataHub.
    3. Vyberte Trezor klíčů – * * * * *
    4. V části nastavení vyberte zásady přístupu.
    5. Vyberte **Přidat zásady přístupu** .
    6. Použití **správy tajných** kódů pro konfiguraci ze šablony a přidání sami sebe do objektu zabezpečení
    7. Vyberte **Přidat**a pak na stránce **zásady přístupu** vyberte **Uložit** .
    8. V části **Nastavení** vyberte **tajná klíčová** .
    9. Vybrat **Sentinel-Password**
    10. Vytvořte novou verzi hodnoty a povolte ji.

- Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro rozsah dat 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Ověřovací centrum: nesprávná adresa URL nebo web není přístupný

**Zpráva o selhání úlohy**: "bohužel došlo k nějakému problému. Stránka, ke které jste se pokusili získat přístup, je (dočasně) nedostupná.

**Nápravná opatření**:

1. Otevřete v prohlížeči [Sentinel](https://scihub.copernicus.eu/dhus/) a zjistěte, jestli je web přístupný.
2. Pokud web není přístupný, zkontrolujte, jestli brána firewall, podniková síť nebo jiný blokující software nebrání přístupu k webu, a pak proveďte potřebné kroky, aby se povolila adresa URL ověřovacího připojení. 
3. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro datum v rozsahu 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.  

### <a name="sentinel-server-down-for-maintenance"></a>Server Sentinel: vypnutí z důvodu údržby

**Zpráva o selhání úlohy**: "centrum přístupu Copernicus otevřené bude brzy! Omlouváme se za nepříjemnosti. v tuto chvíli provádíme údržbu. Brzy se vrátíme do online režimu! " 

**Nápravná opatření**:

K tomuto problému může dojít, pokud se na serveru Sentinel provádí nějaké aktivity údržby.

1. Pokud dojde k chybě nějaké úlohy nebo kanálu, protože probíhá údržba, odešlete úlohu znovu po nějaké době. 

   Informace o všech plánovaných a neplánovaných aktivitách údržby ověřovacích zpráv najdete na webu [Copernicus otevřít centrum Access Hub](https://scihub.copernicus.eu/news/) .  

2. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro datum v rozsahu 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: dosáhlo se maximálního počtu připojení.

**Zpráva o selhání úlohy**: maximální počet dvou souběžných toků dosažených uživatelem \<username> .

**Význam**: Pokud se úloha nezdařila, protože bylo dosaženo maximálního počtu připojení, je stejný účet Sentinel používán ve více úlohách.

**Nápravná opatření**: zkuste jednu z těchto možností:

* Než znovu spustíte neúspěšnou úlohu, počkejte, než se dokončí další úlohy.
* Vytvořte nový účet Sentinel a aktualizujte uživatelské jméno a heslo Sentinel v FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Ověřovací server: odmítnuté připojení

**Zpráva o selhání úlohy**: Server odmítl připojení v: http://172.30.175.69:8983/solr/dhus .

**Nápravná opatření**: k tomuto problému může dojít, pokud se na serveru Sentinel provádí nějaké aktivity údržby.

1. Pokud dojde k chybě nějaké úlohy nebo kanálu, protože probíhá údržba, odešlete úlohu znovu po nějaké době.

   Informace o všech plánovaných a neplánovaných aktivitách údržby ověřovacích zpráv najdete na webu [Copernicus otevřít centrum Access Hub](https://scihub.copernicus.eu/news/) .  

2. Znovu spusťte neúspěšnou úlohu nebo spusťte úlohu satelitních indexů pro datum v rozsahu 5 až 7 dní a potom zkontrolujte, jestli je úloha úspěšná.

### <a name="soil-moisture-map-has-white-areas"></a>Mapa vlhkosti půdy má bílé oblasti

**Problém**: byla vygenerována **Mapa vlhkosti půdy** , ale mapa má hlavně bílé oblasti.

**Nápravná opatření**: k tomuto problému může dojít, pokud se satelitní indexy generované pro čas, pro který byla mapa požadována, NDVI hodnoty, které jsou menší než 0,3. Další informace najdete [v technické příručce z Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Spusťte úlohu znovu pro jiný rozsah dat a ověřte, jestli jsou hodnoty NDVI ve satelitních indexech větší než 0,3.

## <a name="collect-logs-manually"></a>Shromažďovat protokoly ručně

[Nainstalujte a nasaďte Průzkumník služby Azure Storage]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Shromažďování protokolů úloh Azure Data Factory nebo protokolů App Service v DataHub

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Do **vyhledávacího** pole vyhledejte skupinu prostředků FarmBeats DataHub.
3. Na řídicím panelu **skupiny prostředků** vyhledejte účet úložiště *datahublogs \* * . Například *datahublogsmvxmq*.  
4. Ve sloupci **název** vyberte účet úložiště pro zobrazení řídicího panelu **účtu úložiště** .
5. V podokně **datahubblogs \* ** vyberte **otevřít v Průzkumníkovi** , aby se zobrazila **otevřená aplikace Průzkumník služby Azure Storage** .
6. V levém podokně vyberte **kontejnery objektů BLOB**a potom vyberte **protokoly úloh** pro protokoly Azure Data Factory nebo protokoly **appinsights** pro protokoly App Service.
7. Vyberte **Stáhnout** a stáhnout protokoly do místní složky na vašem počítači.

    ![Snímek obrazovky zobrazující stažené soubory protokolu](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Shromažďovat protokoly úloh Azure Data Factory nebo protokoly App Service pro akcelerátor

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Do **vyhledávacího** pole vyhledejte skupinu prostředků akcelerátoru FarmBeats.
3. Na řídicím panelu **skupiny prostředků** vyhledejte účet úložiště *úložiště \* * . Například *storagedop4k \* *.
4. Vyberte účet úložiště ve sloupci **název** pro zobrazení řídicího panelu **účtu úložiště** .
5. V podokně **úložiště \* ** vyberte **otevřít v průzkumníkovi** a otevřete aplikaci Průzkumník služby Azure Storage.
6. V levém podokně vyberte **kontejnery objektů BLOB**a potom vyberte **protokoly úloh** pro protokoly Azure Data Factory nebo protokoly **appinsights** pro protokoly App Service.
7. Vyberte **Stáhnout** a stáhnout protokoly do místní složky na vašem počítači.

## <a name="high-cpu-usage"></a>Vysoké využití procesoru

**Chyba**: obdržíte e-mailové upozornění, které odkazuje na **vysokou výstrahu o využití procesoru**.

**Nápravná opatření**:

1. Přejít do skupiny prostředků FarmBeats DataHub.
2. Vyberte **službu App Service**.  
3. Přejít na [stránku s cenami](https://azure.microsoft.com/pricing/details/app-service/windows/)horizontálního navýšení kapacity App Service a pak vyberte příslušnou cenovou úroveň.

## <a name="weather-data-job-failures"></a>Selhání úlohy dat počasí

**Chyba**: spouštíte úlohy, abyste získali data o počasí, ale úloha se nezdařila.

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Shromažďovat protokoly pro řešení potíží s chybami úlohy počasí

1. V Azure Portal otevřete skupinu prostředků FarmBeats.
2. Klikněte na službu Data Factory, která je součástí skupiny prostředků. Služba bude obsahovat značku "SKU: DataHub"

> [!NOTE]
> Pokud chcete zobrazit značky služeb v rámci skupiny prostředků, klikněte na Upravit sloupce a přidejte do zobrazení skupiny prostředků značky.

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Snímek obrazovky, který zvýrazní značku SKU: DataHub.":::

3. Na stránce Přehled objektu pro vytváření dat klikněte na **vytvořit a monitorovat**. V prohlížeči se otevře nová karta. Klikněte na **monitorování** .

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Snímek obrazovky, který zvýrazní značku SKU: DataHub.":::

4. Zobrazí se seznam spuštění kanálu, která jsou součástí provádění úlohy počasí. Klikněte na úlohu, pro kterou chcete shromažďovat protokoly.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Snímek obrazovky, který zvýrazní značku SKU: DataHub.":::

5. Na stránce Přehled kanálu se zobrazí seznam spuštění aktivit. Poznamenejte si ID spuštění aktivit, pro které chcete shromažďovat protokoly.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Snímek obrazovky, který zvýrazní značku SKU: DataHub.":::

6. Vraťte se do skupiny prostředků FarmBeats v Azure Portal a klikněte na účet úložiště s názvem **datahublogs-xxxx** .
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Snímek obrazovky, který zvýrazní značku SKU: DataHub.":::

7. Klikněte na **kontejnery**  ->  **adfjobs**. Do vyhledávacího pole zadejte ID běhu úlohy, které jste si poznamenali v kroku 5 výše.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="Snímek obrazovky, který zvýrazní značku SKU: DataHub.":::

8. Výsledek hledání bude obsahovat složku, která obsahuje protokoly týkající se úlohy. Stáhněte si protokoly a odešlete je farmbeatssupport@microsoft.com pro pomoc při ladění problému.
