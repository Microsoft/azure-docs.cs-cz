## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
1. Vyberte virtuální počítač, kterou chcete znovu nasadit a pak vyberte *znovu nasadit* tlačítko *nastavení* okno. Budete muset posunout dolů viz **podpora a řešení potíží** části, která obsahuje tlačítko "Znovu nasadit" jako v následujícím příkladu:
   
    ![Okno virtuálního počítače Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Chcete-li potvrdit operaci, vyberte *znovu nasadit* tlačítka:
   
    ![Opětovné nasazení virtuálního počítače okno](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Stav** virtuálního počítače se změní na *aktualizace* jako připraví virtuální počítač znovu nasadit, jak je znázorněno v následujícím příkladu:
   
    ![Aktualizuje se virtuální počítač](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Stav** pak změní na *počáteční* jako virtuální počítač se spustí na nového hostitele Azure, jak je znázorněno v následujícím příkladu:
   
    ![Virtuální počítač se spouští.](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Po dokončení procesu spouštění virtuálního počítače **stav** vrátí do *systémem*, označující, virtuální počítač má se znovu úspěšně nasadil:
   
    ![Virtuální počítač je spuštěný.](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

