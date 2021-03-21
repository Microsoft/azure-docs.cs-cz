---
title: Řešení běžných aktualizací zařízení pro Azure IoT Hub problémy | Microsoft Docs
description: V tomto dokumentu najdete seznam tipů a triky, které vám pomohou napravit mnoho možných problémů s aktualizací zařízení IoT Hub.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030626"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Aktualizace zařízení pro poradce při potížích s IoT Hub

Tento dokument uvádí některé běžné otázky a problémy, které uživatelé aktualizace zařízení nahlásili. Při aktualizaci zařízení prostřednictvím Public Preview se tato příručka pro řešení potíží pravidelně aktualizuje o nové dotazy a řešení. Pokud narazíte na problém, který se v této příručce pro odstraňování potíží nezobrazí, podívejte se do části [kontaktování podpora Microsoftu](#contact) , kde najdete situaci.

## <a name="importing-updates"></a><a name="import"></a>Import aktualizací

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>Otázka: Mám potíže s připojením instance aktualizace zařízení do instance IoT Hub.
_Ujistěte se prosím, že jsou správně nakonfigurované trasy zpráv IoT Hub podle dokumentace k [aktualizacím prostředků zařízení](./device-update-resources.md) ._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>Otázka: vyskytla se chyba související s rolí (chybová zpráva v Azure Portal nebo Chyba rozhraní API 403).
_Možná nemáte správně nakonfigurovaná oprávnění k přístupu. Ujistěte se prosím, že jste správně nakonfigurovali přístupová oprávnění podle dokumentace pro [řízení přístupu pro aktualizace zařízení](./device-update-control-access.md) ._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>Otázka: objevila se chyba 500 typu při importu obsahu do služby aktualizace zařízení.
_Kód chyby v rozsahu 500 může indikovat problém se službou aktualizace zařízení. Počkejte prosím 5 minut a pak to zkuste znovu. Pokud se tato chyba bude opakovat, postupujte podle pokynů v části [kontaktování podpora Microsoftu](#contact) a zapište žádost o podporu od Microsoftu._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>Otázka: při importu obsahu narazím na kód chyby a chci ho analyzovat.
_Informace o kódech chyb najdete v dokumentaci o [kódech chyb aktualizace zařízení](./device-update-error-codes.md) ._

## <a name="device-failures"></a><a name="device-failure"></a>Selhání zařízení

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>Otázka: Jak můžu zajistit, aby bylo zařízení připojeno k aktualizaci zařízení pro IoT Hub?
_Ověření, jestli je zařízení připojené k aktualizaci zařízení, můžete ověřit tak, že zkontrolujete jeho zobrazení v části "Neseskupená" zařízení v zobrazení dodržování předpisů Azure Portal._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>Otázka: některé z mých zařízení se nepodařilo aktualizovat.
_Existuje mnoho možných hlavních příčin selhání aktualizace zařízení. Ověřte prosím, že zařízení je: 1) připojeno k vaší IoT Hub instanci, 2) připojené k vaší instanci aktualizace zařízení a 3) služba optimalizace doručení (DO) je spuštěná. Pokud pro vaše zařízení platí všechny tři, postupujte podle pokynů v části [kontaktování podpora Microsoftu](#contact) a zapište žádost o podporu od Microsoftu._

## <a name="deploying-an-update"></a><a name="deploy"></a> Nasazení aktualizace

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>Otázka: nasadil (a) jsem aktualizaci na moje zařízení, ale stav dodržování předpisů uvádí, že není na nejnovější aktualizaci. Co bych měl/a dělat?
_Aktualizace stavu dodržování předpisů pro zařízení může trvat až 5 minut. Počkejte prosím, pak zkuste to znovu._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>Otázka: stav nasazení mého zařízení zobrazuje nekompatibilní, co mám dělat?
_Po připojení zařízení k IoT Hub mohlo dojít ke změně vlastností výrobce a modelu cílového zařízení, což způsobí, že se zařízení nyní považuje za nekompatibilní s obsahem aktualizace aktuálního nasazení._

_Podívejte se na [rozhraní adu Core](./device-update-plug-and-play.md) , kde zjistíte, co výrobce a model zařízení hlásí do služby aktualizace zařízení, a ujistěte se, že odpovídá výrobci a modelu, který jste zadali v [manifestu import](./import-concepts.md) nasazeného obsahu aktualizace. Tyto vlastnosti pro dané zařízení můžete změnit pomocí [konfiguračního souboru aktualizace zařízení](./device-update-configuration-file.md)._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>Otázka: zobrazuje se moje nasazení ve fázi "aktivní", ale žádná z mých zařízení neprobíhá s aktualizací. Co bych měl/a dělat?
_Zajistěte, aby v budoucnu nebylo nastaveno počáteční datum nasazení. Když vytvoříte nové nasazení, datum zahájení nasazení bude ve výchozím nastavení nastavené na další den jako ochrana, pokud ho explicitně nezměníte. Můžete buď počkat na doručení počátečního data nasazení, nebo zrušit probíhající nasazení a vytvořit nové nasazení s požadovaným počátečním datem._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>Otázka: Chci seskupit moje zařízení, ale při vytváření skupiny se v rozevíracím seznamu nezobrazí značka.
_Ujistěte se, že jste správně nakonfigurovali trasy zpráv v IoT Hub podle dokumentace pro [prostředky aktualizace zařízení](./device-update-resources.md) . Po nakonfigurování trasy budete muset zařízení označit znovu._

_Další hlavní příčinou může být, že jste před připojením zařízení k aktualizaci zařízení IoT Hub použili značku. Ujistěte se, že je vaše zařízení už připojené k aktualizaci zařízení. Můžete ověřit, jestli je zařízení připojené k aktualizaci zařízení pro IoT Hub tím, že zkontroluje, jestli se v zobrazení dodržování předpisů zobrazuje v části Neseskupená zařízení. Dočasně přidejte značku jiné hodnoty a po připojení zařízení přidejte zamýšlenou značku znovu._

_Pokud používáte službu Device Provisioning Service (DPS), ujistěte se, že vaše zařízení označíte po zřízení, a ne během procesu vytváření zařízení. Pokud jste zařízení během kroku vytváření zařízení označili, budete muset po zřízení zařízení dočasně označit svým zařízením jinou hodnotu a pak znovu přidat zamýšlenou značku._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>Otázka: moje nasazení bylo úspěšně dokončeno, ale nepovedlo se aktualizovat některá zařízení.
_To může být způsobeno chybou na straně klienta v neúspěšných zařízeních. V tomto průvodci odstraňováním potíží se podívejte na část chyby zařízení._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>Otázka: při pokusu o inicializaci nasazení došlo k chybě v uživatelském rozhraní.
_Tato chyba mohla být způsobena chybou služby nebo uživatelského prostředí nebo problémem s oprávněními rozhraní API. Postupujte podle pokynů v části [kontaktování podpora Microsoftu](#contact) a zapište žádost o podporu od Microsoftu._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>Otázka: spustil (a) jsem nasazení, ale nedosahuje koncového stavu.
_Tato chyba mohla být způsobena problémem s výkonem služby, chybou služby nebo chybou klienta. Zkuste prosím nasazení znovu po 10 minutách. Pokud narazíte na stejný problém, přečtěte si protokoly zařízení a v tomto průvodci odstraňováním potíží se podívejte na část chyby zařízení. Pokud se tento problém opakuje, postupujte podle pokynů v části [kontaktování podpora Microsoftu](#contact) a zapište žádost o podporu od Microsoftu._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Stahují se aktualizace na zařízení

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>Otázka: Návody pokračovat v stahování, když se zařízení znovu připojí po určité době odpojení?
_Po obnovení připojení do 24 hodin se stahování automaticky obnoví. Po 24 hodinách bude nutné stažení znovu zahájit uživatelem._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Používání mezipaměti připojené k Microsoft (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>Otázka: Mám potíže při pokusu o nasazení modulu MCC na zařízení IoT Edge.
_Chcete-li nasadit moduly Edge do IoT Edge zařízení, přečtěte si [dokumentaci IoT Edge]() . Můžete zjistit, jestli je modul MCC na zařízení s IoT Edge úspěšně spuštěný, a to tak, že přejdete na http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>Otázka: jedno ze zařízení IoT se pokouší stáhnout aktualizaci prostřednictvím MCC, ale selhává.
_Existuje několik problémů, které by mohly způsobit selhání zařízení IoT při připojování k MCC. Pokud chcete problém diagnostikovat, shromážděte z neúspěšného zařízení protokoly klient a Nginx (pokyny ke shromáždění protokolů klienta najdete v části [kontaktování podpora Microsoftu](#contact) )._

_Vaše zařízení může selhat s vyžádáním obsahu z Internetu, aby předávalo modul MCC, protože adresa URL, kterou používá, není povolená. Chcete-li zjistit, zda Ano, budete muset v Azure Portal kontrolovat proměnné prostředí IoT Edge._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Kontaktování podpora Microsoftu

Pokud narazíte na problémy, které se nedají vyřešit pomocí výše uvedených nejčastějších dotazů, můžete žádost o podporu zažádat pomocí podpora Microsoftu přes rozhraní Azure Portal. V závislosti na tom, kterou kategorii označíte, že problém patří, se může zobrazit výzva ke shromáždění a sdílení dalších dat, která vám pomohou podpora Microsoftu prozkoumat váš problém. 

Pokyny ke shromáždění každého datového typu najdete níže. Pomocí [GetDevices]() můžete vyhledat další informace v odpovědi na datovou část rozhraní API.

Kromě toho mohou být užitečné následující informace pro zúžení hlavní příčiny problému:
* Typ zařízení, které se pokoušíte aktualizovat (Azure Percept, IoT Edge brána, další)
* Jaký typ klienta aktualizace zařízení používáte (na základě bitové kopie, simulátoru na bázi balíčku)
* Jaký operační systém vaše zařízení používá
* Podrobnosti o architektuře vašeho zařízení
* Zda jste úspěšně použili aktualizaci zařízení k aktualizaci zařízení před

Pokud máte k dispozici některou z výše uvedených informací, uveďte ji do svého popisu problému.

### <a name="collecting-client-logs"></a>Shromažďování protokolů klienta

* V zařízení malinu PI existují dvě sady protokolů, které najdete tady:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Pro zabaleného klienta se protokoly nacházejí zde:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* V případě simulátoru jsou protokoly nalezeny zde:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Kódy chyb
Můžete být vyzváni k zadání chybových kódů při hlášení problému souvisejícího s importem aktualizace, selháním zařízení nebo nasazením aktualizace.

Kódy chyb lze získat tak, že si prohlížíte rozhraní [ADUCoreInterface](./device-update-plug-and-play.md) . Informace o tom, jak analyzovat kódy chyb pro samočinnou diagnostiku a řešení potíží, najdete v dokumentaci [kódy chyb aktualizace zařízení](./device-update-error-codes.md) .

### <a name="trace-id"></a>ID trasování
Může se zobrazit výzva k zadání ID trasování při hlášení problému souvisejícího s importem nebo nasazením aktualizace.

ID trasování pro danou akci uživatele můžete najít v odpovědi rozhraní API nebo v části Historie importu Azure Portal uživatelského rozhraní. 

ID trasování pro akce nasazení v současné době jsou dostupná jenom prostřednictvím odpovědi rozhraní API.

### <a name="deployment-id"></a>ID nasazení
Může se zobrazit výzva k zadání ID nasazení při hlášení problému souvisejícího s nasazením aktualizace.

ID nasazení je vytvořeno uživatelem při volání rozhraní API za účelem zahájení nasazení.

V současné době se identifikátory nasazení pro nasazení iniciované z Azure Portal uživatelského rozhraní generují automaticky a nepodporují se uživateli.

### <a name="iot-hub-instance-name"></a>Název instance IoT Hub
Může se zobrazit výzva k zadání názvu instance IoT Hub při hlášení problému souvisejícího se selháním zařízení nebo nasazením aktualizace.

IoT Hub jméno vybere uživatel při prvním zřízení.

### <a name="device-update-account-name"></a>Název účtu aktualizace zařízení
Může se zobrazit výzva k zadání názvu účtu aktualizace zařízení při hlášení problému souvisejícího s importem aktualizace, selháním zařízení nebo nasazením aktualizace.

Název účtu aktualizace zařízení je při prvním přihlášení ke službě vybraný uživatelem. Další informace najdete v dokumentaci k [prostředkům aktualizace zařízení](./device-update-resources.md) .

### <a name="device-update-instance-name"></a>Název instance aktualizace zařízení
Může se zobrazit výzva k zadání názvu instance aktualizace zařízení při hlášení problému souvisejícího s importem aktualizace, selháním zařízení nebo nasazením aktualizace.

Název instance aktualizace zařízení je vybraný uživatelem při prvním zřízení. Další informace najdete v dokumentaci k [prostředkům aktualizace zařízení](./device-update-resources.md) .

### <a name="device-id"></a>ID zařízení
Může se zobrazit výzva k zadání ID zařízení při hlášení problému souvisejícího se selháním zařízení nebo nasazením aktualizace.

ID zařízení je definované zákazníkem při prvním zřízení zařízení. Dá se taky načíst z vlákna zařízení.

### <a name="update-id"></a>ID aktualizace
Může se zobrazit výzva k zadání ID aktualizace při hlášení problému souvisejícího s nasazením aktualizace.

ID aktualizace definuje zákazník při zahájení nasazení.

### <a name="nginx-logs"></a>Protokoly Nginx
Může se zobrazit výzva k zadání protokolů Nginx při hlášení problému souvisejícího s mezipamětí Microsoft.

### <a name="adu-conftxt"></a>ADU-conf.txt
Při hlášení problému souvisejícího s nasazením aktualizace se může zobrazit výzva k zadání konfiguračního souboru aktualizace zařízení (adu-conf.txt).

Konfigurační soubor je nepovinný a vytvoří ho uživatel podle pokynů v dokumentaci [Konfigurace aktualizace zařízení](./device-update-configuration-file.md) .

### <a name="import-manifest"></a>Importovat manifest
Může se zobrazit výzva k zadání souboru manifestu importu při hlášení problému souvisejícího s importem nebo nasazením aktualizace.

Manifest importu je soubor vytvořený zákazníkem při importu obsahu aktualizace do služby aktualizace zařízení.

**[Další krok: Další informace o kódech chyb aktualizace zařízení](.\device-update-error-codes.md)**