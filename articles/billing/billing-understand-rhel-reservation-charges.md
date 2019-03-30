---
title: Sleva za plán rezervaci Red Hat a použití – Azure | Dokumentace Microsoftu
description: Zjistěte, jak Red Hat plán slevy pro Red Hat softwaru na virtuálních počítačích.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: 9eba59bcdc9974519fc1ed37078a205eff28f116
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652991"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Vysvětlení použití slevy na plán rezervaci softwaru Red Hat Linux Enterprise pro Azure

Po nákupu plánu Red Hat Linux se automaticky využije slevy na nasazených Red Hat virtuálních počítačů (VM), které odpovídají rezervace. Plán Red Hat Linux zahrnují náklady na provozování softwaru Red Hat na Virtuálním počítači Azure.

Koupit správného plánu Red Hat Linux, musíte pochopit, jaké Red Hat VMs spustíte a počet virtuálních procesorů v těchto virtuálních počítačů. Použijte následující části vám pomůže identifikovat ze souboru CSV využití, co chcete koupit.

## <a name="discount-applies-to-different-vm-sizes"></a>Sleva za vztahuje na různé velikosti virtuálních počítačů

Stejně jako rezervované instance virtuálních počítačů Red Hat plánu nákupů nabízejí flexibilitu velikost instance. To znamená, že slevy platí i při nasazení virtuálního počítače se počet různých virtuálních procesorů. Sleva se vztahuje na různé velikosti virtuálních počítačů v rámci plánu softwaru.

Sleva závisí na poměr uvedené v následujících tabulkách. Poměr porovnává nároky na relativní pro každého měřiče v této skupině. Poměr závisí na virtuální procesory virtuálních počítačů. Poměr hodnoty použijte k výpočtu určete počet instancí virtuálního počítače získat slevu plán Red Hat Linux.

Například pokud koupit plán pro Red Hat Linux Enterprise Server pro virtuální počítač s 3 nebo 4 virtuální procesory, poměr pro tuto rezervaci je 2. Sleva zahrnují náklady na software Red Hat pro:

- 2 nasazené virtuální počítače s 1 nebo 2 virtuální procesory
- 1 virtuální počítač nasazen s 3 nebo 4 virtuální procesory,
- nebo % 77 0.77 nebo o virtuální počítač s 5 nebo víc virtuálních procesorů.

Poměr 5 nebo víc virtuálních procesorů je 2.6. Proto rezervace pro Red Hat s virtuálním Počítačem s 5 nebo víc virtuálních procesorů se týká pouze část náklady na software, což je přibližně % 77.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Vysvětlení využití virtuálních počítačů Red Hat před zakoupením

Následující tabulky popisují softwarové plány, které si můžete koupit rezervaci pro jejich využití měřičů a poměry pro každý.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure portal marketplace názvy:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (nejnovější lvm)

|Red Hat virtuálního počítače | ID měřiče| Poměr| Příklad velikost virtuálního počítače|
| -------| ------------------------| --- |--- |
|1 – 4 virtuální procesory licence virtuálního počítače|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1 – 4 virtuální procesory licence virtuálního počítače|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1 – 4 virtuální procesory licence virtuálního počítače|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|44 virtuální procesor licencí virtuálního počítače|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|60 virtuální procesor licencí virtuálního počítače|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux for SAP s vysokou DOSTUPNOSTÍ

Název na portálu Azure marketplace:

|Red Hat virtuálního počítače | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- | --- | ------------------------| --- | --- |
|1 – 4 virtuální procesory licence virtuálního počítače |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux s využitím HA

Azure portal marketplace názvy:

|Red Hat virtuálního počítače | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- |------------------------| --- | --- |
|1 – 4 virtuální procesory licence virtuálního počítače|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|víc než 5 virtuálních procesorů licence virtuálního počítače|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Azure portal marketplace názvy:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 pro SAP
- Red Hat Enterprise Linux 7.5 pro SAP

|Red Hat virtuálního počítače | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- |------------------------| --- |--- |
|1 virtuální procesor licencí virtuálního počítače|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 virtuální procesor licencí virtuálního počítače|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 virtuální procesory, licence virtuálního počítače|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 virtuální procesor licencí virtuálního počítače|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|8 virtuálních procesorů licence virtuálního počítače|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|12 virtuálních procesorů licence virtuálního počítače|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 virtuálních procesorů licence virtuálního počítače|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|20 virtuálních procesorů licence virtuálního počítače|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 virtuálních procesorů licence virtuálního počítače|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 virtuálních procesorů licence virtuálního počítače|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|40 virtuálních procesorů licence virtuálního počítače|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|44 virtuální procesor licencí virtuálního počítače|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|60 virtuální procesor licencí virtuálního počítače|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 virtuálních procesorů licence virtuálního počítače|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 virtuální procesor licencí virtuálního počítače|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|96 virtuální procesor licencí virtuálního počítače|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 virtuálních procesorů licence virtuálního počítače|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Azure portal marketplace názvy:

- Red Hat Enterprise Linux 6.7 pro SAP HANA
- Red Hat Enterprise Linux 7.2 pro SAP HANA
- Red Hat Enterprise Linux 7.3 pro SAP HANA

|Red Hat virtuálního počítače | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- |------------------------| --- |--- |
|1 virtuální procesor licencí virtuálního počítače|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 virtuální procesor licencí virtuálního počítače|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 virtuální procesory, licence virtuálního počítače|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 virtuální procesor licencí virtuálního počítače|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|8 virtuálních procesorů licence virtuálního počítače|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|12 virtuálních procesorů licence virtuálního počítače|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 virtuálních procesorů licence virtuálního počítače|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|20 virtuálních procesorů licence virtuálního počítače|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 virtuálních procesorů licence virtuálního počítače|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 virtuálních procesorů licence virtuálního počítače|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|40 virtuálních procesorů licence virtuálního počítače|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|44 virtuální procesor licencí virtuálního počítače|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|60 virtuální procesor licencí virtuálního počítače|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 virtuálních procesorů licence virtuálního počítače|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 virtuální procesor licencí virtuálního počítače|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|96 virtuální procesor licencí virtuálního počítače|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 virtuálních procesorů licence virtuálního počítače|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>Další postup

Další informace o rezervacích, naleznete v následujících článcích:

- [Co jsou rezervace pro Azure](billing-save-compute-costs-reservations.md)
- [Předplatíte plány softwaru Red Hat s Azure rezervace](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa rezervací pro Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).