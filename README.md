# Sample-code
#include "mbed.h"
#include "LSM6DSLSensor.h"  // Make sure this library is added to your project

UnbufferedSerial pc(USBTX, USBRX, 115200); // Serial connection to PC for debugging
I2C i2c(D14, D15); // SDA, SCL pins for your board might differ
LSM6DSLSensor acc_gyro(&i2c, LSM6DSL_ACC_GYRO_I2C_ADDRESS_HIGH); // Adjust the address if necessary

// Function to compute the pitch from accelerometer data
float computeAngle(int ax, int ay, int az) {
    float pitch = atan2(ax, sqrt(ay * ay + az * az)) * 180.0 / 3.14159;
    return pitch;
}

int main() {
    pc.set_format(8, BufferedSerial::None, 1); // Setting serial communication format
    acc_gyro.init();
    acc_gyro.enable_x();

    int16_t data[3]; // Array to hold acceleration data
    while (true) {
        acc_gyro.get_x_axes(data); // Read acceleration data
        float pitch = computeAngle(data[0], data[1], data[2]); // Compute the pitch
        printf("Pitch: %f degrees\n", pitch);
        ThisThread::sleep_for(2000ms); // Wait for 2 seconds
    }
}
