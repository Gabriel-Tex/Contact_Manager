cmake_minimum_required(VERSION 3.28)
project(ContactManager)

add_executable(contactmanager src/main.cpp src/Contact.cpp src/ContactManager.cpp)
target_include_directories(contactmanager PRIVATE include)