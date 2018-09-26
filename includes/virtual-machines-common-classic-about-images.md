

Image se používají Azure k zajištění nového virtuálního počítače s operačním systémem. Image také může mít jednu nebo několik datových disků. Image jsou dostupné z několika zdrojů:

* Azure nabízí imagí v [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/). Nejsou k dispozici nejnovější verze Windows serveru a distribuce operačního systému Linux. Některé Image také obsahují aplikace, jako je SQL Server. Výhoda MSDN a MSDN s průběžnými platbami předplatitelé mají přístup k další Image.
* Open source komunitou nabízí obrázky prostřednictvím [VM Depot](http://vmdepot.msopentech.com/List/Index).
* Také můžete ukládat a používat vlastní Image v Azure, zachycení existujícího virtuálního počítače Azure pro použití jako image nebo nahrávání obrázku.

## <a name="about-vm-images-and-os-images"></a>O imagích virtuálních počítačů a bitové kopie operačního systému
Dva typy imagí je možné v Azure: *image virtuálního počítače* a *image operačního systému*. Image virtuálního počítače obsahuje operační systém a všechny disky připojené k virtuálnímu počítači, když se vytvoří bitovou kopii. Image virtuálního počítače je novější typ bitové kopie. Před zavedením imagí virtuálních počítačů, image v Azure může mít pouze zobecněný operačního systému a žádné další disky. Image virtuálního počítače, který obsahuje pouze zobecněný operační systém je v podstatě stejný jako původní typ image, image operačního systému.

Můžete vytvořit vlastní Image, na základě virtuálního počítače v Azure nebo virtuální počítač spuštěný jinde, který můžete zkopírovat a odeslat. Pokud chcete vytvořit více než jeden virtuální počítač pomocí image, musíte připravit ji pro použití jako image tak, že ji zobecníte. Pokud chcete vytvořit image Windows serveru, spusťte příkaz Sysprep na serveru k zobecnění ho před nahráním souboru VHD. Podrobnosti o nástroji Sysprep najdete v tématu [použití nástroje Sysprep: Úvod](http://go.microsoft.com/fwlink/p/?LinkId=392030) a [podpory nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Zálohování virtuálního počítače před spuštěním nástroje Sysprep. Vytváří se image Linuxu se liší podle distribuce. Obvykle budete muset spustit sadu příkazů, které jsou specifické pro distribuci a spuštění agenta Azure Linux.
