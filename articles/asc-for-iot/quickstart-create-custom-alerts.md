---
title: Vytvořit vlastní upozornění ASC pro náhled IoT | Dokumentace Microsoftu
description: Vytvořte a přiřaďte zařízení vlastní výstrahy pro ASC pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: fa1825d6f6f6ea5e6c026791aa70dd1b37bdad90
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574732"
---
# <a name="quickstart-create-custom-alerts"></a>Rychlý start: Vytvořit vlastní upozornění

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Použití výstrah a vlastní skupiny zabezpečení, plně využijte informace o zabezpečení začátku do konce a znalostí zařazené do kategorií zařízení k zajištění lepší zabezpečení v řešení IoT. 

## <a name="why-use-custom-alerts"></a>Proč používat vlastní upozornění? 

Znáte nejlepší zařízení IoT.

Pro zákazníky, kteří plně porozumět jejich zařízení očekávané chování ASC pro IoT umožňuje přeložit tyto znalosti do zásad chování zařízení a upozornění na jakékoli odchylky od očekávaného, běžné chování.

## <a name="security-groups"></a>Skupiny zabezpečení

Skupiny zabezpečení umožňují definovat logické skupiny zařízení a spravovat jejich stavu centralizované způsobem.

Tyto skupiny může představovat zařízení s konkrétním hardwarovým nasazené do určitého umístění nebo libovolné jiné skupiny, které jsou vhodné k vašim konkrétním potřebám.

Skupiny zabezpečení jsou definovány pomocí vlastnosti značku dvojčete modulu zabezpečení s názvem **skupiny SecurityGroup**. Změňte hodnotu této vlastnosti se změnit skupinu zabezpečení zařízení.  

Ve výchozím nastavení, každé řešení IoT na centrum IoT má jednu skupinu zabezpečení s názvem **výchozí**.

Pomocí skupin zabezpečení k seskupení zařízení do logických kategorií. Po vytvoření skupiny, je přiřadíte k vlastní výstrahy podle vašeho výběru pro nejúčinnější-ucelené řešení. 

## <a name="customize-an-alert"></a>Přizpůsobení upozornění

1. Otevřete své Centrum IoT. 
2. Vyberte **zabezpečení**a pak vyberte **vlastní výstrahy**. 
3. Zvolte, kterou chcete použít vlastní nastavení pro skupiny zabezpečení. 
4. Klikněte na tlačítko **přidat vlastní upozornění**
5. Zadejte název výstrahy (Všimněte si, že názvy výstrah nelze změnit po vytvoření). 
6. Z rozevíracího seznamu vyberte vlastní chování výstrah. 
7. Upravit požadované vlastnosti, klikněte na tlačítko **OK**.
8. Ujistěte se, že klikněte na tlačítko **Uložit**. Bez uložení nové výstrahy, oznámení se odstraní při příštím zavřete služby IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Výstrahy, které jsou k dispozici pro přizpůsobení

Následující tabulka nabízí souhrn výstrah, které jsou k dispozici pro přizpůsobení.

| Severity | Název                                                                                                    | Zdroj dat | Popis                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Nízká      | Vlastní výstraha – počet cloudu na zařízení zprávy v protokolu AMQP není v povoleném rozsahu          | IoT Hub     | Množství cloudu do zařízení zpráv (AMQP protocol) v časové okno není v nakonfigurovaných povolený rozsah                                  |
| Nízká      | Vlastní upozornění - počet odmítnutých cloudu na zařízení zprávy v protokolu AMQP není v povoleném rozsahu | IoT Hub     | Velikost zprávy typu zařízení (protokolu AMQP), které byly odmítnuty přenosem zařízení v časové okno v cloudu se nenachází v nakonfigurovaných povolený rozsah |
| Nízká      | Vlastní výstraha – počet zařízení na cloud zprávy v protokolu AMQP není v povoleném rozsahu          | IoT Hub     | Množství zařízení na cloud zprávy (protokolu AMQP) v časové okno není v nakonfigurovaných povolený rozsah                                  |
| Nízká      | Vyvolá vlastní výstraha – počet přímé metody není v povoleném rozsahu                              | IoT Hub     | Vyvolá množství přímé metody v okamžiku okno není v nakonfigurovaných povolený rozsah                                                     |
| Nízká      | Vlastní výstraha – počet nahrávání souborů není v povoleném rozsahu                                       | IoT Hub     | Množství nahrávání souborů v časové okno není v nakonfigurovaných povolený rozsah                                                              |
| Nízká      | Vlastní výstraha – počet cloudu na zařízení zprávy v protokolu HTTP není v povoleném rozsahu          | IoT Hub     | Množství cloudu na zařízení zprávy (protokol HTTP) v časové okno není v nakonfigurovaných povolený rozsah                                  |
| Nízká      | Vlastní upozornění - počet odmítnutých cloudu na zařízení zprávy v protokolu HTTP není v povoleném rozsahu | IoT Hub     | Velikost zprávy typu zařízení (protokol HTTP), které byly odmítnuty přenosem zařízení v časové okno v cloudu se nenachází v nakonfigurovaných povolený rozsah |
| Nízká      | Vlastní výstraha – počet zařízení na cloud zprávy v protokolu HTTP není v povoleném rozsahu          | IoT Hub     | Množství zařízení na cloud zprávy (protokol HTTP) v časové okno není v nakonfigurovaných povolený rozsah                                  |
| Nízká      | Vlastní výstraha – počet cloudu na zařízení zprávy v protokolu MQTT není v povoleném rozsahu          | IoT Hub     | Množství cloudu na zařízení zprávy (protokolu MQTT) v časové okno není v nakonfigurovaných povolený rozsah                                  |
| Nízká      | Vlastní upozornění - počet odmítnutých cloudu na zařízení zprávy v protokolu MQTT není v povoleném rozsahu | IoT Hub     | Velikost zprávy typu zařízení (protokolu MQTT), které byly odmítnuty přenosem zařízení v časové okno v cloudu se nenachází v nakonfigurovaných povolený rozsah |
| Nízká      | Vlastní výstraha – počet zařízení na cloud zprávy v protokolu MQTT není v povoleném rozsahu          | IoT Hub     | Množství zařízení na cloud zprávy (protokolu MQTT) v časové okno není v nakonfigurovaných povolený rozsah                                  |
| Nízká      | Vlastní výstraha – počet vymazat frontu příkaz není v povoleném rozsahu                               | IoT Hub     | Vymaže množství frontu příkazů v čase, které okno není v nakonfigurovaných povolený rozsah                                                      |
| Nízká      | Vlastní výstraha – počet aktualizace dvojčat není v povoleném rozsahu                                       | IoT Hub     | Množství aktualizace dvojčat v časové okno není v nakonfigurovaných povolený rozsah                                                              |
| Nízká      | Vlastní výstraha – počet neautorizovaných operací není v povoleném rozsahu                            | IoT Hub     | Množství neautorizovaných operací v časové okno není v nakonfigurovaných povolený rozsah                                                   |
| Nízká      | Vlastní výstraha – počet aktivních připojení není v povoleném rozsahu                                        | Agent       | Počet aktivních připojení v časové okno není v nakonfigurovaných povolený rozsah                                                        |
| Nízká      | Vytvoření vlastní výstraha – odchozí připojení pro integrační balíček, který není povolen                              | Agent       | Odchozí připojení pro integrační balíček, který není povolen, někdo vytvořil                                                                                  |
| Nízká      | Vlastní výstrahy – počet neúspěšných přihlášení místní není v povoleném rozsahu                                | Agent       | Množství neúspěšných místní přihlášení v časové okno není v nakonfigurovaných povolený rozsah                                                       |
| Nízká      | Vlastní výstraha – přihlašovací jméno uživatele, který není povolen                                                      | Agent       | Místní uživatel, který není povoleno přihlášení k zařízení                                                                                        |
| Nízká      | Vlastní výstraha – spuštění procesu, který není povolen                                               | Agent       | Spustil se proces, který není povolen v zařízení |          |

## <a name="next-steps"></a>Další postup

Přejděte k dalším článku se dozvíte, jak nasadit agenta zabezpečení...

> [!div class="nextstepaction"]
> [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
