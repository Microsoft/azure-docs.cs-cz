---
title: Slevy rezervačních plánů pro Red Hat – Azure
description: Zjistěte, jak se na software Red Hat na virtuálních počítačích uplatňují slevy za plán Red Hat.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 173967356c17150d5694b48e6ed2acd78155ca4b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199224"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Vysvětlení způsobu uplatňování slevy za plán rezervace softwaru Red Hat Linux Enterprise v Azure

Jakmile si zakoupíte plán Red Hat Linux, na nasazené virtuální počítače se softwarem Red Hat vyhovující rezervaci se začne automaticky uplatňovat sleva. Plán Red Hat Linux pokrývá náklady na chod softwaru Red Hat na virtuálním počítači Azure.

Pokud si chcete koupit správný plán Red Hat Linux, potřebujete mít přehled o tom, jaké virtuální počítače Red Hat používáte a jaký je počet vCPU na těchto virtuálních počítačích. Následující části vám na základě vašeho souboru CSV s informacemi o využití pomůžou určit, který plán si máte koupit.

## <a name="discount-applies-to-different-vm-sizes"></a>Sleva se vztahuje na různé velikosti virtuálních počítačů

Podobně jako je to u rezervovaných instancí virtuálních počítačů, i plány Red Hat nabízejí flexibilitu velikosti instance. To znamená, že vaše sleva platí i v případě, že nasadíte virtuální počítač s jiným počtem vCPU. Sleva se vztahuje na různé velikosti virtuálních počítačů v rámci softwarového plánu.

Výše slevy závisí na koeficientu uvedeném v následujících tabulkách. Koeficient zohledňuje relativní využití paměti pro každý měřič v dané skupině. Koeficient závisí na počtu vCPU virtuálního počítače. Pomocí koeficientu můžete vypočítat, kolik instancí virtuálních počítačů bude mít nárok na slevu za plán Red Hat Linux.

Pokud si například zakoupíte plán softwaru Red Hat Linux Enterprise Server pro virtuální počítač se 3 nebo 4 vCPU, má daná rezervace koeficient 2. Sleva pokryje náklady na software Red Hat pro:

- 2 nasazené virtuální počítače s 1 nebo 2 vCPU
- 1 nasazený virtuální počítač se 3 nebo 4 vCPU
- Nebo 0,77, tedy asi 77 % virtuálního počítače s 5 nebo více vCPU

Koeficient při 5 nebo více vCPU činí 2,6. Z toho důvodu rezervace softwaru Red Hat pro virtuální počítač s 5 nebo více vCPU pokrývá jen určitou část nákladů na software, zhruba ve výši 77 %.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Před nákupem si zjistěte využití virtuálních počítačů se softwarem Red Hat

Následující tabulky představují softwarové plány, na které si můžete koupit rezervaci, související měřiče využití a příslušné koeficienty.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Názvy z Marketplace na webu Azure Portal:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (nejnovější LVM)

|Virtuální počítač se softwarem Red Hat | MeterId| Koeficient| Velikost ukázkového virtuálního počítače|
| -------| ------------------------| --- |--- |
|Licence na virtuální počítač s 1–4 vCPU|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|Licence na virtuální počítač s 1–4 vCPU|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|Licence na virtuální počítač s 1–4 vCPU|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|Licence na virtuální počítač s 5 a více vCPU|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|037eddc0-fedd-4d73-b5d8-92fba9edb831|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|794dcb90-0793-43e6-9909-70d29974e56d|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|86c35ec3-0a48-426a-9625-22d80e6ea55b|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2,166666667|D8s_v3|
|Licence na virtuální počítač s 5 a více vCPU|d09f877e-03b4-48b2-b11a-782b965cff19|2,166666667|D8s_v3|
|Licence na virtuální počítač se 44 vCPU|6f44ae85-a70e-44be-83ec-153a0bc23979|2,166666667||
|Licence na virtuální počítač se 60 vCPU|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2,166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux pro SAP s HA

Název z Marketplace na webu Azure Portal:

|Virtuální počítač se softwarem Red Hat | MeterId | Koeficient|Velikost ukázkového virtuálního počítače|
| ------- | --- | ------------------------| --- | --- |
|Licence na virtuální počítač s 1–4 vCPU |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|Licence na virtuální počítač s 5 a více vCPU|6dfb482b-23ea-487f-810c-e66360f025de|2,333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux s HA

Názvy z Marketplace na webu Azure Portal:

|Virtuální počítač se softwarem Red Hat | MeterId | Koeficient|Velikost ukázkového virtuálního počítače|
| ------- |------------------------| --- | --- |
|Licence na virtuální počítač s 1–4 vCPU|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|Licence na virtuální počítač s 5 a více vCPU|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Názvy z Marketplace na webu Azure Portal:

- Red Hat Enterprise Linux 6.8 pro SAP Business Apps
- Red Hat Enterprise Linux 7.3 pro SAP Business Apps
- Red Hat Enterprise Linux 7.4 pro SAP
- Red Hat Enterprise Linux 7.5 pro SAP

|Virtuální počítač se softwarem Red Hat | MeterId | Koeficient|Velikost ukázkového virtuálního počítače|
| ------- |------------------------| --- |--- |
|Licence na virtuální počítač s 1 vCPU|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|Licence na virtuální počítač se 2 vCPU|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|Licence na virtuální počítač se 4 vCPU|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|Licence na virtuální počítač se 6 vCPU|69a140fa-e08e-415c-85f2-48158e4c73a0|2,166666667||
|Licence na virtuální počítač s 8 vCPU|777a5a74-22d6-48c9-9705-ac38fe05a278|2,166666667|D8s_v3|
|Licence na virtuální počítač s 12 vCPU|d6b8917a-5127-497a-9f48-1e959df98812|2,166666667||
|Licence na virtuální počítač s 16 vCPU|03667e82-e009-425a-83f7-8ebddbca5af4|2,166666667|D16s_v3|
|Licence na virtuální počítač s 20 vCPU|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2,166666667||
|Licence na virtuální počítač se 24 vCPU|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2,166666667|ND24s|
|Licence na virtuální počítač se 32 vCPU|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2,166666667|D32s_v3|
|Licence na virtuální počítač se 40 vCPU|737142c3-8e4f-4fc1-aa41-05b1661edff8|2,166666667||
|Licence na virtuální počítač se 44 vCPU|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2,166666667||
|Licence na virtuální počítač se 60 vCPU|a22bb342-ba9a-4529-a178-39a92ce770b6|2,166666667||
|Licence na virtuální počítač se 64 vCPU|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2,166666667|64s_v3|
|Licence na virtuální počítač s 72 vCPU|14341b96-e92c-4dca-ba66-322c88a79aa6|2,166666667|F72s_v2|
|Licence na virtuální počítač s 96 vCPU|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2,166666667||
|Licence na virtuální počítač se 128 vCPU|9b198a68-974a-47a7-9013-49169ac0f2e9|2,166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Názvy z Marketplace na webu Azure Portal:

- Red Hat Enterprise Linux 6.7 pro SAP HANA
- Red Hat Enterprise Linux 7.2 pro SAP HANA
- Red Hat Enterprise Linux 7.3 pro SAP HANA

|Virtuální počítač se softwarem Red Hat | MeterId | Koeficient|Velikost ukázkového virtuálního počítače|
| ------- |------------------------| --- |--- |
|Licence na virtuální počítač s 1 vCPU|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|Licence na virtuální počítač se 2 vCPU|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|Licence na virtuální počítač se 4 vCPU|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|Licence na virtuální počítač se 6 vCPU|a5963812-0f5a-4053-8ace-2b5babd15ed8|2,166666667||
|Licence na virtuální počítač s 8 vCPU|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2,166666667|D8s_v3|
|Licence na virtuální počítač s 12 vCPU|0e3bc72d-a888-4bcf-8437-119f763a3215|2,166666667||
|Licence na virtuální počítač s 16 vCPU|b40e95d8-3176-42f0-967c-497785c031b2|2,166666667|D16s_v3|
|Licence na virtuální počítač s 20 vCPU|81f34277-499d-40a3-a634-99adc08e2d45|2,166666667||
|Licence na virtuální počítač se 24 vCPU|e03f1906-d35d-4084-b2cd-63281869c8ee|2,166666667|ND24s|
|Licence na virtuální počítač se 32 vCPU|0a58c082-ceb8-4327-9b64-887c30dddb23|2,166666667|D32s_v3|
|Licence na virtuální počítač se 40 vCPU|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2,166666667||
|Licence na virtuální počítač se 44 vCPU|378b8125-d8a5-4e09-99bc-c1462534ffb0|2,166666667||
|Licence na virtuální počítač se 60 vCPU|5d7db11a-54e9-404e-aaa8-509fac7c0638|2,166666667||
|Licence na virtuální počítač se 64 vCPU|3c8157b2-a57d-45ce-ba02-bd86e9209795|2,166666667|64s_v3|
|Licence na virtuální počítač s 72 vCPU|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2,166666667|F72s_v2|
|Licence na virtuální počítač s 96 vCPU|b13895fc-0d06-4de9-b860-627c471cd247|2,166666667||
|Licence na virtuální počítač se 128 vCPU|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2,166666667| M128ms|

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích najdete v následujících článcích:

- [Co jsou rezervace v Azure](save-compute-costs-reservations.md)
- [Předplacení plánů softwaru Red Hat pomocí rezervací Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa rezervací v Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
