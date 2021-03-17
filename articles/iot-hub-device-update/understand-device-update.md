---
title: Úvod do aktualizace zařízení pro Azure IoT Hub | Microsoft Docs
description: Aktualizace zařízení pro IoT Hub je služba, která umožňuje nasazení OTA (Over-The-Air) pro vaše zařízení IoT.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 60dfd448a66ca67a241f97570c91f683323a7d6d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232371"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Přehled aktualizace zařízení pro IoT Hub (Preview)

Aktualizace zařízení pro IoT Hub je služba, která umožňuje nasazení OTA (Over-The-Air) pro vaše zařízení IoT.

Vzhledem k tomu, že organizace hledají lepší produktivitu a provozní efektivitu, řešení Internet věcí (IoT) i nadále budou přijímány rostoucími sazbami. V podstatě je to proto, že zařízení tvořící Tato řešení jsou založená na základu spolehlivosti a zabezpečení a je snadné je připojit a spravovat ve velkém měřítku. Aktualizace zařízení pro IoT Hub je koncová platforma, kterou můžou zákazníci použít k publikování, distribuci a správě bezletových aktualizací pro všechno od drobných senzorů až po zařízení na úrovni brány. 

Aby bylo možné využít všechny výhody digitální transformace s podporou IoT, zákazníci potřebují tuto možnost provozovat, udržovat a aktualizovat škálovaná zařízení. Prozkoumejte výhody implementace aktualizace zařízení pro IoT Hub, které zahrnují schopnost rychle reagovat na hrozby zabezpečení a nasazovat nové funkce, aby se získaly obchodní cíle, aniž by to mělo za sebou dodatečné náklady na vývoj a údržbu při vytváření vlastních aktualizačních platforem.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Podpora široké škály zařízení IoT


Aktualizace zařízení pro IoT Hub je navržená tak, aby nabízela optimalizované nasazení aktualizací a zjednodušila operace prostřednictvím integrace s [Azure IoT Hub](https://azure.microsoft.com/en-us/services/iot-hub/). Tato integrace usnadňuje přijetí aktualizace zařízení v jakémkoli existujícím řešení. Poskytuje řešení hostované v cloudu pro připojení prakticky jakéhokoli zařízení. Aktualizace zařízení podporuje širokou škálu operačních systémů IoT, včetně Linux a [Azure RTO](https://azure.microsoft.com/en-us/services/rtos/) (operační systém v reálném čase) – a je rozšiřitelná prostřednictvím open source. Spoluvyvíjíme aktualizace zařízení pro IoT Hub nabídky s našimi partnery, jako jsou STMicroelectronics, NXP, Reneseas a mikročip. Seznamte se s [ukázkami](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) karet pro vyhodnocení klíčů, které obsahují příručky Začínáme, a Naučte se, jak konfigurovat, sestavovat a nasazovat Ota aktualizace na zařízení MCU třídy. 

K dispozici jsou i binární soubory simulátoru agenta aktualizace zařízení a dokumentace k malinu PI reference Yocto.
Aktualizace zařízení pro IoT Hub podporuje také aktualizaci Azure IoT Edgech zařízení. Agent aktualizace zařízení je k dispozici pro platformu Ubuntu Server 18,04 amd64. Aktualizace zařízení pro IoT Hub také poskytuje open-source kód, pokud nepoužíváte některou z výše uvedených platforem. Agenta můžete přenést na distribuci, kterou používáte.

Aktualizace zařízení funguje se službou IoT technologie Plug and Play (PnP) a může spravovat všechna zařízení, která podporují požadovaná rozhraní PnP. Další informace najdete v tématu [aktualizace zařízení pro IoT Hub a IoT technologie Plug and Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Podpora široké škály artefaktů aktualizací

Aktualizace zařízení pro IoT Hub podporuje dvě formy aktualizací – založené na bitových kopiích a na balíčcích.

Aktualizace založené na balíčku jsou cílené aktualizace, které mění pouze konkrétní součást nebo aplikaci na zařízení. To vede ke snížení spotřeby šířky pásma a pomáhá zkrátit dobu potřebné ke stažení a instalaci aktualizace. Aktualizace balíčků obvykle umožňují méně výpadkům zařízení při použití aktualizace a vyhněte se režii při vytváření imagí.

Aktualizace obrázků poskytují v konečném stavu zařízení vyšší úroveň spolehlivosti. Obvykle je snazší replikovat výsledky aktualizace image mezi předprodukčním prostředím a produkčním prostředím, protože nepředstavuje stejné výzvy jako balíčky a jejich závislosti.
Z důvodu atomické povahy může jeden model převzetí služeb při selhání A/B také snadno přijmout.

Neexistuje žádná pravá odpověď a můžete si vybrat odlišně v závislosti na konkrétních případech použití. Aktualizace zařízení pro IoT Hub podporuje jak image, tak i balíčky aktualizací, což vám umožní zvolit si správný model aktualizace pro vaše prostředí zařízení.

## <a name="flexible-features-for-updating-devices"></a>Flexibilní funkce pro aktualizaci zařízení

Aktualizace zařízení pro funkce IoT Hub poskytují výkonné a flexibilní prostředí, včetně těchto:

* Správa uživatelského rozhraní pro správu aktualizací integrovaných s Azure IoT Hub
* Postupná aktualizace pro zavedení prostřednictvím seskupování zařízení a aktualizace ovládacích prvků plánování
* Programová rozhraní API pro povolení prostředí automatizace a vlastního portálu
* Stavová zobrazení a kompatibilita s aktualizacemi v rámci loďstva zařízení heterogenní
* Podpora odolných aktualizací zařízení (A/B) pro zajištění bezproblémového vrácení zpět
* Přístup k předplatným a řízení přístupu na základě rolí dostupných prostřednictvím portálu Azure.com
* Podpora místní mezipaměti obsahu a vnořené hraniční podpory, aby bylo možné aktualizovat zařízení odpojená v cloudu
* Podrobné nástroje pro správu aktualizací a vytváření sestav 

Díky aktualizaci zařízení pro řízení IoT Hub správy a nasazení můžou uživatelé maximalizovat produktivitu a ušetřit tak cenné čas. Aktualizace zařízení pro IoT Hub zahrnuje možnost seskupovat zařízení a určit, která zařízení by měla být nasazena aktualizace. Uživatelé si taky můžou zobrazit stav nasazení aktualizací a ujistit se, že každé zařízení úspěšně používá aktualizace.

Pokud dojde k chybě aktualizace, aktualizace zařízení pro IoT Hub také umožňuje uživatelům identifikovat zařízení, u kterých se nepodařilo použít aktualizaci, a zobrazit související podrobnosti o selhání. Možnost určit, která zařízení se nepovedlo aktualizovat, znamená, že se dlouhé ručně uložené hodiny při pokusu o určení zdroje.

### <a name="best-in-class-security-at-global-scale"></a>Nejlepší zabezpečení ve své třídě v globálním měřítku

Microsoft Azure podporuje více než miliarda zařízení IoT po celém světě – počet, který je rychle narůstající na den. Aktualizace zařízení pro IoT Hub na tomto prostředí sestaví a prověřenou spolehlivost, kterou ukázala web Windows Updateá platforma, takže zařízení je možné bez problémů aktualizovat v globálním měřítku.

Aktualizace zařízení pro IoT Hub využívá komplexní zabezpečení na úrovni cloudu, které je vyvíjené pro Microsoft Azure, takže zákazníci nepotřebují strávit čas potřebný k jejich sestavování sami od základů.


## <a name="device-update-workflows"></a>Pracovní postupy aktualizace zařízení

Funkce aktualizace zařízení může být rozdělená na tři oblasti: integrace agenta, import a správa.

### <a name="device-update-agent"></a>Agent aktualizace zařízení

Při přijetí příkazu k aktualizaci na zařízení se spustí požadovaná fáze aktualizace (stáhnout, nainstalovat a použít). Během každé fáze se stav vrátí do aktualizace zařízení prostřednictvím IoT Hub, takže můžete zobrazit aktuální stav nasazení. Pokud neprobíhá žádná aktualizace, je stav vráceno jako nečinné. Nasazení lze kdykoli zrušit.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagram pracovního postupu agenta aktualizace zařízení." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Přečtěte si další informace](device-update-agent-overview.md) o agentovi aktualizace zařízení. 

### <a name="importing"></a>Import

Import je způsob, jakým se aktualizace ingestují do aktualizace zařízení, aby je bylo možné nasadit do zařízení. Aktualizace zařízení podporuje zavedení jedné aktualizace na jedno zařízení. To je ideální pro aktualizace kompletních imagí, které aktualizují celý oddíl operačního systému najednou, nebo apt manifest, který popisuje všechny balíčky, které chcete na svém zařízení aktualizovat. Pokud chcete importovat aktualizace do aktualizace zařízení, nejdřív vytvořte manifest importu popisující aktualizaci a pak nahrajte soubory aktualizací a importujte manifest do umístění přístupného pro Internet. Potom můžete pomocí Azure Portal nebo [aktualizace zařízení importovat REST API](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) zahájit asynchronní proces importu aktualizace. Aktualizace zařízení nahraje soubory, zpracuje je a zpřístupňuje k distribuci do zařízení IoT.

V případě citlivého obsahu Chraňte stahování pomocí sdíleného přístupového podpisu (SAS), jako je například ad-hoc SAS pro Azure Blob Storage. [Další informace o SAS](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagram aktualizace zařízení pro IoT Hub Import pracovního postupu" lightbox="media/understand-device-update/import-update.png":::

[Přečtěte si další informace](import-concepts.md) o importu aktualizací. 

### <a name="grouping-and-deployment"></a>Seskupení a nasazení

Po importu aktualizace můžete zobrazit kompatibilní aktualizace pro vaše zařízení a třídy zařízení.

Aktualizace zařízení podporuje koncept **skupin** prostřednictvím značek v IoT Hub. Nasazování aktualizace na testovací skupinu je dobrým způsobem, jak snížit riziko problémů během uvedení do provozu.

V rámci aktualizace zařízení představují nasazení způsob připojení správného obsahu ke konkrétní sadě kompatibilních zařízení. Aktualizace zařízení orchestrují proces posílání příkazů na každé zařízení, který dává pokyn ke stažení a instalaci aktualizací a získání stavu zpět.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagram aktualizace zařízení pro pracovní postup seskupení a nasazení IoT Hub" lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Další informace](device-update-compliance.md) o konceptech nasazení

[Další informace](device-update-groups.md) o skupinách aktualizací zařízení


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření účtu a instance aktualizace zařízení](create-device-update-account.md)
