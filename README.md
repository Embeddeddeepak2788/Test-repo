#include <stdio.h>
#include <stdbool.h>
#include <stdint.h>
#include <inttypes.h>

typedef struct{
    uint16_t c_codes[18];//!< Cell Voltage Codes    
    uint8_t pec_match[6];//!< If a PEC error was detected during most recent read cmd
}cv;
//! AUX Reg Voltage Data
typedef struct{ 
    short a_codes[9];//!< Aux Voltage Codes
    char pec_match[4];//!< If a PEC error was detected during most recent read cmd
}ax;

typedef struct{
     int stat_codes[4];//!< A two dimensional array of the stat voltage codes.
     int flags[3]; //!< byte array that contains the uv/ov flag data
     int mux_fail[1]; //!< Mux self test status flag
     int thsd[1]; //!< Thermal shutdown status
     int pec_match[2];//!< If a PEC error was detected during most recent read cmd
}st;

typedef struct{
int tx_data[6];
int rx_data[8];
int rx_pec_match;//!< If a PEC error was detected during most recent read cmd
}ic_register;

typedef struct{
    int pec_count;
    int cfgr_pec;
    int cell_pec[6];
    int aux_pec[4];
    int stat_pec[2];
}pec_counter;

typedef struct{
    int cell_channels; 
    int stat_channels;
    int aux_channels;
    int num_cv_reg; 
    int num_gpio_reg;
    int num_stat_reg;
}register_cfg;

typedef struct{
    
    ic_register config;
    ic_register configb;
    cv   cells;
    ax   aux;
    st   stat;
    ic_register  com;
    ic_register pwm;
    ic_register pwmb;
    ic_register sctrl;
    ic_register sctrlb;
    bool isospi_reverse;
    pec_counter crc_count;
    register_cfg ic_reg;
    long system_open_wire;
}cell_asic;
//Write the ltc681x CFGRA 
void ltc681x_wrcfg(int total_ic, //The number of ICs being written to
                     cell_asic ic[] 
                    )
{
    
    int write_buffer[256];
    int write_count = 0;
    int c_ic = 0;
    for(int current_ic = 0; current_ic<total_ic;current_ic++)
    {
        for(int data = 0; data<6;data++)
        {
            if(ic->isospi_reverse == true){c_ic = current_ic;}
            else{c_ic = total_ic - current_ic - 1;}
            
            write_buffer[write_count] = ic[c_ic].config.tx_data[data];
            printf("%X\n",write_buffer[write_count]);
            write_count++;
            
        }
    }
    
}
#define TOTAL_IC  1
cell_asic bms_ic[TOTAL_IC];
int main()
{
    int buffer[6];
 
printf("SIZE OF cell_asic = %d\n",sizeof(bms_ic)); 
printf("SIZE OF buffer  = %d\n",sizeof(buffer));
//ltc681x_wrcfg( TOTAL_IC,bms_ic);
return 0;
}
