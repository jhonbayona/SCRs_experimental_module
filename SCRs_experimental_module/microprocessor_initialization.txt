#include "PeripheralHeaderIncludes.h"
#pragma CODE_SECTION(InitFlash, "ramfuncs");
#define Device_cal (void   (*)(void))0x3D7C80
void DeviceInit(void);
void PieCntlInit(void);
void PieVectTableInit(void);
void WDogDisable(void);
void PLLset(Uint16);
void ISR_ILLEGAL(void);
void DeviceInit(void)
{
	WDogDisable();
	DINT;
	IER = 0x0000;
	IFR = 0x0000;
	EALLOW;
	SysCtrlRegs.PCLKCR0.bit.ADCENCLK = 1;
	(*Device_cal)();
	SysCtrlRegs.PCLKCR0.bit.ADCENCLK = 0;
	EDIS;
	EALLOW;
	SysCtrlRegs.CLKCTL.bit.INTOSC1OFF = 0;
    SysCtrlRegs.CLKCTL.bit.OSCCLKSRCSEL=0;
	SysCtrlRegs.CLKCTL.bit.XCLKINOFF=1;
	SysCtrlRegs.CLKCTL.bit.XTALOSCOFF=1;
	SysCtrlRegs.CLKCTL.bit.INTOSC2OFF=1;
    EDIS;
	PLLset( 0x10 );
	PieCntlInit();
	PieVectTableInit();
   EALLOW;
   SysCtrlRegs.LOSPCP.all = 0x0002;
   SysCtrlRegs.XCLK.bit.XCLKOUTDIV=2;
   SysCtrlRegs.PCLKCR0.bit.ADCENCLK = 1;
   SysCtrlRegs.PCLKCR0.bit.I2CAENCLK = 0;
   SysCtrlRegs.PCLKCR0.bit.SPIAENCLK=0;
   SysCtrlRegs.PCLKCR0.bit.SPIBENCLK = 0;
   SysCtrlRegs.PCLKCR0.bit.MCBSPAENCLK = 0;
   SysCtrlRegs.PCLKCR0.bit.SCIAENCLK=0;
   SysCtrlRegs.PCLKCR0.bit.SCIBENCLK = 0;
   SysCtrlRegs.PCLKCR1.bit.EPWM1ENCLK = 1;
   SysCtrlRegs.PCLKCR1.bit.EPWM2ENCLK = 1;
   SysCtrlRegs.PCLKCR1.bit.EPWM3ENCLK = 1;
   SysCtrlRegs.PCLKCR1.bit.EPWM4ENCLK = 1;
   SysCtrlRegs.PCLKCR1.bit.EPWM5ENCLK = 1;
   SysCtrlRegs.PCLKCR1.bit.EPWM6ENCLK = 1;
   SysCtrlRegs.PCLKCR1.bit.EPWM7ENCLK = 1;
   SysCtrlRegs.PCLKCR1.bit.EPWM8ENCLK = 1;
   SysCtrlRegs.PCLKCR0.bit.TBCLKSYNC = 1;
   SysCtrlRegs.PCLKCR3.bit.DMAENCLK = 0;
   SysCtrlRegs.PCLKCR3.bit.CLA1ENCLK = 0;
   SysCtrlRegs.PCLKCR3.bit.COMP1ENCLK = 1;
   SysCtrlRegs.PCLKCR3.bit.COMP2ENCLK = 1;
   SysCtrlRegs.PCLKCR3.bit.COMP3ENCLK = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO0 = 1;
	GpioCtrlRegs.GPADIR.bit.GPIO0 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO2 = 1;
	GpioCtrlRegs.GPADIR.bit.GPIO2 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO4 = 1;
	GpioCtrlRegs.GPADIR.bit.GPIO4 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO6 = 1;
	GpioCtrlRegs.GPADIR.bit.GPIO6 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO8 = 1;
	GpioCtrlRegs.GPADIR.bit.GPIO8 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO10 = 1;
	GpioCtrlRegs.GPADIR.bit.GPIO10 = 1;
	GpioCtrlRegs.GPBMUX1.bit.GPIO40 = 1;
	GpioCtrlRegs.GPBDIR.bit.GPIO40 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO12 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO13 = 1;
	GpioCtrlRegs.GPAMUX1.bit.GPIO14 = 1;
	GpioCtrlRegs.GPAMUX2.bit.GPIO16 = 0;
	GpioCtrlRegs.GPADIR.bit.GPIO16 = 0;
	GpioIntRegs.GPIOXINT2SEL.bit.GPIOSEL=16;
	GpioCtrlRegs.GPAMUX2.bit.GPIO17 = 0;
	GpioCtrlRegs.GPADIR.bit.GPIO17 = 0;
	GpioCtrlRegs.GPAMUX2.bit.GPIO18 = 0;
	GpioCtrlRegs.GPADIR.bit.GPIO18 = 1;
	GpioCtrlRegs.GPAMUX2.bit.GPIO19 = 0;
	GpioCtrlRegs.GPADIR.bit.GPIO19 = 0;
	GpioCtrlRegs.GPBMUX1.bit.GPIO32 = 0;
	GpioCtrlRegs.GPBDIR.bit.GPIO32 = 0;
	GpioCtrlRegs.GPBMUX1.bit.GPIO33 = 0;
	GpioCtrlRegs.GPBDIR.bit.GPIO33 = 1;
	GpioDataRegs.GPBCLEAR.bit.GPIO33 = 1;
	GpioCtrlRegs.GPBMUX1.bit.GPIO34 = 0;
	GpioCtrlRegs.GPBDIR.bit.GPIO34 = 1;
	GpioDataRegs.GPBCLEAR.bit.GPIO34 = 1;
	EDIS;
}
void WDogDisable(void)
{
    EALLOW;
    SysCtrlRegs.WDCR= 0x0068;
    EDIS;
}
void PLLset(Uint16 val)
{
   volatile Uint16 iVol;
   if (SysCtrlRegs.PLLSTS.bit.MCLKSTS != 0)
   {
	  EALLOW;
      SysCtrlRegs.PLLSTS.bit.MCLKCLR = 1;
      EDIS;
      asm("        ESTOP0");
   }
   if (SysCtrlRegs.PLLSTS.bit.DIVSEL != 0)
   {
       EALLOW;
       SysCtrlRegs.PLLSTS.bit.DIVSEL = 0;
       EDIS;
   }
   if (SysCtrlRegs.PLLCR.bit.DIV != val)
   {

      EALLOW;
      SysCtrlRegs.PLLSTS.bit.MCLKOFF = 1;
      SysCtrlRegs.PLLCR.bit.DIV = val;
      EDIS;
      WDogDisable();
      while(SysCtrlRegs.PLLSTS.bit.PLLLOCKS != 1) {}
      EALLOW;
      SysCtrlRegs.PLLSTS.bit.MCLKOFF = 0;
	  EDIS;
	  }
	  EALLOW;
      SysCtrlRegs.PLLSTS.bit.DIVSEL = 2;
      EDIS;
}
void PieCntlInit(void)
{
    DINT;
    PieCtrlRegs.PIECTRL.bit.ENPIE = 0;
	PieCtrlRegs.PIEIER1.all = 0;
	PieCtrlRegs.PIEIER2.all = 0;
	PieCtrlRegs.PIEIER3.all = 0;
	PieCtrlRegs.PIEIER4.all = 0;
	PieCtrlRegs.PIEIER5.all = 0;
	PieCtrlRegs.PIEIER6.all = 0;
	PieCtrlRegs.PIEIER7.all = 0;
	PieCtrlRegs.PIEIER8.all = 0;
	PieCtrlRegs.PIEIER9.all = 0;
	PieCtrlRegs.PIEIER10.all = 0;
	PieCtrlRegs.PIEIER11.all = 0;
	PieCtrlRegs.PIEIER12.all = 0;

	PieCtrlRegs.PIEIFR1.all = 0;
	PieCtrlRegs.PIEIFR2.all = 0;
	PieCtrlRegs.PIEIFR3.all = 0;
	PieCtrlRegs.PIEIFR4.all = 0;
	PieCtrlRegs.PIEIFR5.all = 0;
	PieCtrlRegs.PIEIFR6.all = 0;
	PieCtrlRegs.PIEIFR7.all = 0;
	PieCtrlRegs.PIEIFR8.all = 0;
	PieCtrlRegs.PIEIFR9.all = 0;
	PieCtrlRegs.PIEIFR10.all = 0;
	PieCtrlRegs.PIEIFR11.all = 0;
	PieCtrlRegs.PIEIFR12.all = 0;
}
void PieVectTableInit(void)
{
	int16 i;
   	PINT *Dest = &PieVectTable.TINT1;

   	EALLOW;
   	for(i=0; i < 115; i++)
    *Dest++ = &ISR_ILLEGAL;
   	EDIS;

   	PieCtrlRegs.PIECTRL.bit.ENPIE = 1;
}

interrupt void ISR_ILLEGAL(void)
{
  asm("          ESTOP0");
  for(;;);

}

void InitFlash(void)
{
   EALLOW;
   FlashRegs.FOPT.bit.ENPIPE = 1;
   FlashRegs.FBANKWAIT.bit.PAGEWAIT = 3;
   FlashRegs.FBANKWAIT.bit.RANDWAIT = 3;
   FlashRegs.FOTPWAIT.bit.OTPWAIT = 5;
   FlashRegs.FSTDBYWAIT.bit.STDBYWAIT = 0x01FF;
   FlashRegs.FACTIVEWAIT.bit.ACTIVEWAIT = 0x01FF;
   EDIS;
   asm(" RPT #7 || NOP");
}
void MemCopy(Uint16 *SourceAddr, Uint16* SourceEndAddr, Uint16* DestAddr)
{
    while(SourceAddr < SourceEndAddr)
    {
       *DestAddr++ = *SourceAddr++;
    }
    return;
}










