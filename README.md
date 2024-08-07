# Software in the Loop (SITL)

The SITL (software in the loop) simulator allows you to run Plane, Copter or Rover without any hardware. It is a build of the autopilot code using an ordinary C++ compiler, giving you a native executable that allows you to test the behaviour of the code without hardware.
## Vehicle creation, connection and control
1. Open 3 Linux windows and enter each of their virtual environments. The first one is for dronekit-sitl to create vehicle. The second one is for mavproxy to broadcast vehicle to your scripts and Mission Planner. The third one is for python scripts to control the vehicle.
   ```
   \source ~/virtual_env/venv_with_python3.7/bin/activate
   ```   
   ![IMAGE ALT TEXT HERE](Image/3windows.jpg)

3. Create the vehicle in the first window:
   ```
   dronekit-sitl copter --home=5.147036,100.493809,0,180
   ```
   Copter is the mode of vehicle. --home HOME set home location (Latitude, Longitude, Altitude, yaw)
   For more informations, please check [DroneKit-sitl](https://github.com/dronekit/dronekit-sitl) 
   ![IMAGE ALT TEXT HERE](Image/Dronekit_sitl.jpg)
   
5. Broadcaste the vehicle in the second window:
   By typing ipconfig in Windows Command Prompt you can get IPV4 Ip address)
   IPV4 address:
   ![IMAGE ALT TEXT HERE](Image/Ipconfig.jpg)
   ```
   mavproxy.py --master tcp:127.0.0.1:5760 --out udp:127.0.0.1:14540 --out udp:172.28.208.1:14550
   ```
   For –master tcp:127.0.0.1:5760, it connects mavproxy with virtual drone 
   For --out udp:127.0.0.1:14551, it adds new port for being connected with python scripts.
   For --out udp:172.28.208.1:14550, it adds new port for being connected with Missionplaner in your Windows system. (172.28.208.1 should be IPv4 address of WSL)
   Run mavproxy with IPV4 address:
   ![IMAGE ALT TEXT HERE](Image/MAVProxy.jpg)

7. Now, if you run MP, you will see it automatically connect to the vehicle.
   ![IMAGE ALT TEXT HERE](Image/MP_connection.jpg)
   
8. Enter the SITL for folder to run the simple_goto.py in the third window.
   ```
   cd dronekit-python/examples/SITL/
   ```
   ```
   python simple_goto.py --connect udp:127.0.0.1:14540
   ```
   --connect udp: is used to specify the port for the connection between the script and vehicle.
   ![IMAGE ALT TEXT HERE](Image/Simple_goto_commandline.jpg)
   ![IMAGE ALT TEXT HERE](Image/vehicle_fly.jpg)

## Assignment

1. Changing the home location, target location as you want. Be careful with the sleep time! You can pick it up from Google Maps, but watch out for the space character.
2. Changing the mode to Return To Launch (RTL) by  modifying the python script to let the drone fly back to the home point after every flights.
3. Modifying the script to start the autonomous mission by manually setting Guided mode.
   ```
   while vehicle.mode.name != "xxx":
    print(f"Current mode: {vehicle.mode.name}, waiting to switch to xxx mode...")
    time.sleep(3)
   ```
5. Adding target points allows the drone to fly along a specified shape trajectory, such as a triangle, square, or pentagram.
6. Creating multiple vehicles and arranging them d in special shapes, such as four vehicles fly to the four vertices of a square.
You can use this command to transfer the file in Windows to Linux.
```
cp -rf "/mnt/c/SITL" ~/dronekit-python/examples/
```
Hints for assignment no. 4:
To create a new vehicle and broadcaste it, you need to open two more Linux windows.

1. You can add -I1 to starts a new vehicle in tcp:127.0.0.1:5770. (if you add -I2, then it will starts a new vehicle in tcp:127.0.0.1:5780 and -I3 for tcp:127.0.0.1:5790 and so on):
   ```
   dronekit-sitl copter -I1 --home=5.147036,100.493809,0,180
   ```
   
2. Use mavproxy to connect with new vehicle and broadcast it. Be careful not to conflict with other vehicle ports  
   ```
   mavproxy.py --master tcp:127.0.0.1:5770 --out udp:127.0.0.1:14541 –out udp:172.23.0.1:14551
   ```
   Look carefully at the port changes in the command!
   
3. In simple_goto.py, you should add code to connect the scripts with the new vehicle:
   vehicle2 = connect("udp:127.0.0.1:14552", wait_ready=True)
   The first parameter specifies the target address (in this case the loopback address for UDP port 14550).
   The second parameter (wait_ready) is used to determine whether connect() returns immediately on connection or if it waits until some vehicle parameters and attributes are populated. In most cases you should use wait_ready=True to wait on the default set of parameters.
   
4. If you want to control the vehicle2 to go, you should add following command in simple_goto.py:
   arm_and_takeoff(vehicle2, 10)
   point2 = LocationGlobalRelative(5.145999, 100.493804, 15)
   vehicle2.simple_goto(point2, groundspeed=15)

5. Actually, you also can create and broadcast the vehicle in scripts, please check [Swarm_creation.py](Swarm_creation.py)，then you are avoid to open so many windows.
![IMAGE ALT TEXT HERE](Image/Simple_goto_two_connection.jpg)
![IMAGE ALT TEXT HERE](Image/Simple_goto_two_moving.jpg)


## License
This project is released under the MIT License. Please review the [License file](LICENSE1.txt) for more details.
