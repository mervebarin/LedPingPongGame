# LedPingPongGame 
#Read it in the Raw form

Merve Barın 
Doğanay Şirintuna 

ELEC LAB PROJECT REPORT
Introduction
In this project we were asked to implement a two-paddle game in which the Ping-Pong ball is modeled by sequential illumination of the LEDs in two directions. The paddles are push buttons, which must be pressed at just the right time to return the ball. 
A Brief Explanation of the Game
In order to show the moving ball, we used 8 different Leds. We used two push buttons for the paddles of the users. In order for the game to start the users first push their button at the same time. Both of the users have the right to serve the ball twice in a row then the serve right changes. If a user pushes the button at the right time, the ball changes the direction, if a user pushes at the wrong time, the other user gets the point and the score board updates itself. The game finishes when one of the user’s score reaches 10. When a user’s score reaches 10, all leds turn on then off. We added a reset button in order to stop the game and start it from the beginning. When user 1 wins the seven-segment display part of the FPGA board shows 1111, which means user 1 has won. If user 2 wins then the board shows 2222, which means user 2 has won the game. 

Conclusion
In this project we learned how to display four different number independently on a FPGA board and updating it when one of the user scores.  We used four bit variable to show different led configurations. To make the changes in this states in the order which we wanted and to be able to see all four segment of the seven-segment display part of the FPGA board, we used clock cycle. This game starts when two paddles, which are the middle push buttons on the FPGA board, pushed at the same time. The ball, the first led, starts from left and goes to right with time. If right user pushes at the right time the ball starts going left. If right one can’t push the button at the right time, left user gets the point and scoreboard updates the scores. The ball starts from left twice, then starts from right twice and this goes on until one of the users reaches 10 points. When someone wins the scoreboard shows which of the user has won. While writing the code we had some issues while showing the scores on the scoreboard because we first couldn’t find a way to change each segment of the board independently. We have overcome this issue with using a counter and a clock. The lights of the each segment turn on and off so quickly that our eyes couldn’t see when the lights were turned off. So all four display parts looks as turned on at the same time but changes independently.

Our VHDL Code for the Game
----------------------------------------------------------------------------------
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.STD_LOGIC_ARITH.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;

---- Uncomment the following library declaration if instantiating
---- any Xilinx primitives in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity ledpingpongcode is

port(
signal CLK: in STD_LOGIC;
signal clk1: in STD_LOGIC;
signal Fleds: out STD_LOGIC_VECTOR(7 downto 0);
signal Reset: in STD_LOGIC:='0';
signal User1: in STD_LOGIC:='0';
signal User2: in STD_LOGIC:='0';
signal segment_disp :out std_logic_vector(6 downto 0);
signal display: out std_logic_vector(3 downto 0));

end ledpingpongcode;

architecture Behavioral of ledpingpongcode is

signal score:STD_LOGIC_VECTOR(1 downto 0);
signal R04:STD_LOGIC_VECTOR(3 downto 0);
signal R14:STD_LOGIC_VECTOR(3 downto 0);
signal FF: STD_LOGIC_VECTOR(3 downto 0):="0000";
signal R0, R1,L0,L1,sayac,serve: integer :=0;
signal state_display:STD_LOGIC_VECTOR(3 downto 0);
signal state :STD_LOGIC_VECTOR(3 downto 0);
signal L04:STD_LOGIC_VECTOR(3 downto 0);
signal L14:STD_LOGIC_VECTOR(3 downto 0);

begin
process(CLK)

begin

if(rising_edge(CLK)) then 

if(FF="0000" and User1='1' and User2='1' and serve<2)then
serve<=serve+1;
FF<="0010";
end if;

if (Reset='1') then
R1<=0;
R0<=0;
L1<=0;
L0<=0;
serve<=0;
end if;

if (FF="1111")then
FF<="0000";
end if;

if(FF="0000" and User1='1' and User2='1' and 1<serve)then
serve<=serve+1;
FF<="1001";
if(2<serve) then
serve<=0;
end if;
end if;

if(((FF = "0000" or FF = "0010" or FF = "0011" or FF = "0100" or FF = "0101" or FF = "0110" or FF = "0111" or FF = "1001" or FF = "1010" or FF = "1011" or FF = "1100" or FF = "1101" or FF = "1110") and User1='0' and User2='0' ) or (FF="1000" and (User1='0' and User2='1')) or (FF="0001" and (User1='1' and User2='0'))) then
FF(3)<=(FF(2) and FF(1) and FF(0)) xor FF(3);
FF(2)<=(FF(1) and FF(0)) xor FF(2);
FF(1)<=(FF(0)) xor FF(1);
FF(0)<=not(FF(0));
end if;

if (FF="0001" and User2='1') then
FF<="1111";
L0<=L0+1;
if(L0>8) then
R1<=1;
R0<=1;
L1<=1;
L0<=1;
end if;
end if;

if (FF="0001" and User1='0') then
FF<="1111";
R0<=R0+1;
if(R0>8) then
R1<=2;
R0<=2;
L1<=2;
L0<=2;
end if;
end if;

if(FF="1000" and User1='1')then
FF<="1111";
R0<=R0+1;
if(R0>8) then
R1<=2;
R0<=2;
L1<=2;
L0<=2;
end if;
end if;

if (FF="1000" and User2='0') then
FF<="1111";
L0<=L0+1;
if(L0>8) then
R1<=1;
R0<=1;
L1<=1;
L0<=1;
end if;
end if;

if((FF = "0010" or FF = "0011" or FF = "0100" or FF = "0101" or FF = "0110" or FF = "0111" or FF = "1001" or FF = "1010" or FF = "1011" or FF = "1100" or FF = "1101" or FF = "1110" or FF = "1111") and (User1='1' and User2='0'))then
FF<="1111";
R0<=R0+1;
if(R0>8) then
R1<=2;
R0<=2;
L1<=2;
L0<=2;
end if;
end if;

if((FF = "0010" or FF = "0011" or FF = "0100" or FF = "0101" or FF = "0110" or FF = "0111" or FF = "1001" or FF = "1010" or FF = "1011" or FF = "1100" or FF = "1101" or FF = "1110" or FF = "1111") and (User1='0' and User2='1'))then
FF<="1111";
L0<=L0+1;
if(L0>8) then
R1<=1;
R0<=1;
L1<=1;
L0<=1;
end if;
end if;

if(FF="1110" and User1='0' and User2='0') then
FF<="0001";
end if;

if (FF="0000" and (User1='0' or User2='0')) then
FF<="0000";
end if;

end if;

R04<=conv_std_logic_vector(R0,4);
R14<=conv_std_logic_vector(R1,4);
L04<=conv_std_logic_vector(L0,4);
L14<=conv_std_logic_vector(L1,4);

end process;

process(clk1)
begin
if(rising_edge(clk1)) then
sayac <=sayac+1;
if(sayac = 100) then
score(1 downto 0) <= score(1 downto 0) + 1 ;
sayac<=0;
case score(1 downto 0) is 
when "00" => state <=R04 ; state_display <= "1110" ;
when "01" => state <=R14; state_display <= "1101" ;
when "10" => state <=L04 ; state_display <= "1011" ;
when others => state <=L14 ; state_display <= "0111" ;

end case;

case state is
when "0000"=>segment_disp<= "1000000";   
when "0001"=>segment_disp<= "1111001"; 
when "0010"=>segment_disp<= "0100100";
when "0011"=>segment_disp<= "0110000"; 
when "0100"=>segment_disp<= "0011001"; 
when "0101"=>segment_disp<= "0010010";
when "0110"=>segment_disp<= "0000010";
when "0111"=>segment_disp<= "1111000";
when "1000"=>segment_disp<= "0000000";
when "1001"=>segment_disp<= "0010000";  
when others=>segment_disp<= "0000000";
end case ; 
display<= state_display;

end if;
end if;
end process;

-------------------------------------------

With FF select 
Fleds<="10000000" when "0001",
"01000000" when "0010",
"00100000" when "0011",
"00010000" when "0100",
"00001000" when "0101",
"00000100" when "0110",
"00000010" when "0111",
"00000001" when "1000",
"00000010" when "1001",
"00000100" when "1010",
"00001000" when "1011",
"00010000" when "1100",
"00100000" when "1101",
"01000000" when "1110",
"11111111" when "1111",
"00000000" when others;

end Behavioral;




