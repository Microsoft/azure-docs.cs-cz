---
title: Nejčastější dotazy k Centru zabezpečení Azure pro IoT| Dokumenty společnosti Microsoft
description: Najděte odpovědi na nejčastější dotazy týkající se funkcí a služeb Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 639a3f89e470a832279add8d2ed7cf49441611f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571800"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Nejčastější dotazy ke službě Azure Security Center pro IoT  

Tento článek obsahuje seznam nejčastějších dotazů a odpovědí týkajících se Centra zabezpečení Azure pro IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>Poskytuje Azure podporu pro zabezpečení IoT?

Azure poskytuje integrované zobrazení pro monitorování a správu zabezpečení IoT jako součást celkového řešení zabezpečení prostřednictvím Azure Security Center. Pokud jste vývojář aplikací, můžete ke správě zabezpečení aplikací IoT služby IoT Hub použít zobrazení služby IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Jaká je jedinečná nabídka hodnoty Azure pro zabezpečení IoT?

Azure Security Center pro IoT umožňuje podnikům rozšířit své stávající zobrazení kybernetického zabezpečení na celé jejich řešení IoT. Azure poskytuje komplexní zobrazení vašeho obchodního řešení, které vám umožní přijímat obchodní akce a rozhodnutí na základě stavu zabezpečení podniku a shromážděných dat. Kombinované zabezpečení pomocí Azure IoT, Azure IoT Edge a Azure Security Center vám umožní vytvořit požadované řešení s požadovaným zabezpečením, které potřebujete.

## <a name="who-is-azure-security-center-for-iot-made-for"></a>Pro koho je Azure Security Center pro IoT určen? 

Azure Security Center for IoT je integrované v azure iot hub zabezpečení a poskytuje správu pro každodenní operace zabezpečení obchodních řešení. Azure Security Center pro IoT je taky integrované do funkcí Azure Security Center a poskytuje integrované zobrazení pro monitorování a správu zabezpečení IoT jako součást celkového řešení zabezpečení.

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>Jak azure security center pro IoT v porovnání s konkurencí?

Zatímco jiná řešení poskytují sadu funkcí, které zákazníkům umožňují vytvářet vlastní řešení, Azure Security Center for IoT poskytuje jedinečné komplexní řešení zabezpečení IoT, které poskytuje široký přehled o zabezpečení všech souvisejících Azure Zdroje. Azure umožňuje rychlé nasazení a úplnou integraci s dvojčaty modulů IoT Hub pro snadnou integraci se stávajícími nástroji pro správu zařízení.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Musím být zákazníkem Azure Security Center, abych mohl tuto službu používat?

Ne, ale doporučuje se to. Bez Azure Security Center, Azure Security Center pro IoT obdrží omezená data připojených prostředků a poskytuje omezenou analýzu potenciálního útoku povrchu, hrozby a potenciální útoky. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Musím být zákazníkem Azure IoT?

Ano. Azure Security Center pro IoT závisí na připojení Azure IoT a infrastruktury.

## <a name="do-i-have-to-install-an-agent"></a>Musím nainstalovat agenta?

Instalace agenta na vašich zařízeních IoT není povinná, aby bylo možné povolit Centrum zabezpečení Microsoft Azure pro IoT. Můžete si vybrat mezi následujícími třemi možnostmi, které podle vašeho výběru získají různé úrovně monitorování a správy zabezpečení:

1. Nainstalujte Azure Security Center pro ioT agent zabezpečení s nebo bez úprav. Tato možnost poskytuje nejvyšší úroveň rozšířené hospo- bezpečnostní přehledy o chování zařízení a přístupu.

2. Vytvořte si vlastního agenta a implementujte schéma zpráv zabezpečení Centra zabezpečení Microsoft Azure pro IoT. Tato možnost umožňuje použití nástrojů pro analýzu Zabezpečení Microsoft Azure pro IoT nad agentem zabezpečení zařízení.

3. Žádná instalace agenta zabezpečení na vašich zařízeních IoT. Tato možnost umožňuje monitorování komunikace služby IoT Hub se sníženými možnostmi monitorování a správy zabezpečení. 

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>K čemu dělá Azure Security Center pro agenta IoT?

Azure Security Center pro agenta IoT poskytuje pokrytí ohrožení na úrovni zařízení pro konfiguraci zařízení, chování a přístup (skenováním konfigurace), proces & připojení. Azure Security Center pro agenta zabezpečení IoT neprohledá obchodní data nebo aktivity.

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>Kde můžu získat Azure Security Center pro agenta zabezpečení IoT?

Azure Security Center pro ioT agent zabezpečení je open source a je k dispozici na GitHubu v 32bitových a 64bitových verzích pro Windows a Linux: https://github.com/Azure/Azure-Security-IoT.

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>Kde se nainstaluje Azure Security Center for IoT agent? 

Podrobné informace o instalaci a nasazení agenta najdete na GitHubu: https://github.com/Azure/Azure-IoT-Security.

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Jaké jsou závislosti a požadavky agenta?

Azure Security Center pro IoT podporuje širokou škálu platforem. Informace o podpoře konkrétních zařízení najdete v [tématu Platformy podporovaných zařízení.](how-to-deploy-agent.md) 

## <a name="which-data-is-collected-by-the-agent"></a>Jaká data agent shromažďuje?

Připojení, přístup, konfigurace brány firewall, seznam procesů & směrný plán operačního systému jsou shromažďovány agentem.

## <a name="how-much-data-will-the-agent-generate"></a>Kolik dat bude agent generovat?

Generování dat agenta je řízeno zařízením, aplikací, typem připojení a konfigurací agenta zákazníka. Vzhledem k vysoké variabilitě mezi zařízeními a řešeními IoT doporučujeme agenta nejprve nasadit v testovacím prostředí nebo testovacínastavení, abyste mohli sledovat, učit se a nastavovat konkrétní konfiguraci, která vyhovuje vašim potřebám, a zároveň měřit množství generovaných dat. Po spuštění služby poskytuje agent Azure Security Center for IoT provozní doporučení pro optimalizaci propustnosti agentů, která vám pomohou s procesem konfigurace a přizpůsobení.

## <a name="how-can-i-control-my-billing"></a>Jak mohu kontrolovat svou fakturaci?

Azure Security Center pro IoT poskytuje konfigurovatelné agentprohledávky, datové vyrovnávací paměti a možnost vytvářet vlastní výstrahy, které zvyšují nebo snižují množství dat generovaných agentem.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Směřují zprávy agenta do kvóty z ioT hubu?

Ano. Data přenášená agentem se počítají ve vaší kvótě centra IoT Hub. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Co dál? Nainstaloval i agenta a nevidím žádné aktivity nebo protokoly...

1. Zkontrolujte [typ agenta, který odpovídá určené platformě operačního prostoru vašeho zařízení](how-to-deploy-agent.md)

1. Zkontrolujte, zda [je agent spuštěn v zařízení](how-to-agent-configuration.md).

2. Zkontrolujte, zda [služba byla úspěšně povolena](quickstart-onboard-iot-hub.md) **zabezpečení** ve vašem Centru IoT. 

3. Zkontrolujte, zda je zařízení [nakonfigurované v centru IoT Hub pomocí modulu Azure Security Center for IoT](quickstart-create-security-twin.md).  

Pokud aktivity nebo protokoly stále nejsou k dispozici, obraťte se na partnera Azure Security Center pro IoT další pomoc.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co se stane, když přestane fungovat připojení k internetu?

Agent nadále spouštět a ukládat data tak dlouho, dokud je zařízení spuštěno. Data jsou uložena v mezipaměti zpráv zabezpečení podle konfigurace velikosti. Jakmile zařízení znovu získá připojení, zprávy zabezpečení se znovu odešlou. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Pokud je zařízení restartováno, obnoví se agent zabezpečení sám?

Agent zabezpečení je navržen tak, aby se automaticky znovu spouštěl při každém restartování zařízení.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Může agent ovlivnit výkon zařízení nebo jiného nainstalovaného softwaru?

Agent spotřebovává prostředky počítače jako jakoukoli jinou aplikaci nebo proces a neměl by narušit normální aktivitu zařízení. Spotřeba prostředků v zařízení, na které agent běží, je spojena s jeho nastavením a konfigurací. Doporučujeme testování konfigurace agenta v uzavřeném prostředí, spolu s interoperabilitou s ostatními aplikacemi a funkcemi IoT, před pokusem o nasazení v produkčním prostředí.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Dělám nějakou údržbu na zařízení. Můžu vypnout agenta?

Agenta nelze vypnout.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existuje způsob, jak otestovat, zda agent pracuje správně? 

Pokud agent přestane komunikovat nebo se nezdaří odeslat zprávy zabezpečení, **zařízení je tichá** výstraha je generována.

## <a name="can-i-create-my-own-alerts"></a>Mohu si vytvořit vlastní upozornění?

Ano. Můžete nastavit vlastní výstrahu na předem určenou sadu chování, jako je ip adresa a otevřené porty. Další informace o vlastních výstrahách a jejich vytváření najdete v tématu [Vytváření vlastních výstrah.](quickstart-create-custom-alerts.md) 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Kde mohu zobrazit protokoly? Mohu protokoly přizpůsobit?

- Zobrazení upozornění a doporučení pomocí připojeného pracovního prostoru Log Analytics Nakonfigurujte velikost úložiště a dobu trvání v pracovním prostoru.

- Nezpracovaná data od agenta zabezpečení mohou být také uložena ve vašem účtu Log Analytics. Před změnou konfigurace této možnosti zvažte velikost, dobu trvání, požadavky na úložiště a související náklady. 

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>Proč bych měl přidat Azure Security Center pro IoT do identity modulu? K čemu se používá?

Modul Azure Security Center for IoT se používá pro konfiguraci a správu agentů.


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít s Azure Security Center pro IoT, najdete v následujících článcích:


- Přečtěte si [přehled](overview.md) Centra zabezpečení Azure pro IoT
- Ověření [požadavků služby](service-prerequisites.md)
- Další informace o [tom,](getting-started.md) jak začít
- Principy [výstrah zabezpečení Centra zabezpečení Azure pro IoT](concept-security-alerts.md)

