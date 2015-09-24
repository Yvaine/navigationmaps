#include <iostream>
#include <algorithm>
#include <random>
#include <string>
#include "LatLon.h"
#include "m1.h"
#include "m2.h"
#include "graphics.h"
#include "StreetsDatabaseAPI.h"
#include <unittest++/UnitTest++.h>
#include "unit_test_util.h"

using namespace std;

SUITE(unit_tests_for_m2)
{
    TEST(FIND_CLOSEST_INTERSECTION)
    {
        std::string test_intersection = "Lawrence Avenue East & Scarborough Golf Club Road";
        unsigned int_id = find_intersection_id_from_name(test_intersection);
        LatLon pos = getIntersectionPosition(int_id);
        
        //correct test#1
        std::string intersection_name = find_closest_intersection(pos);
        CHECK_EQUAL(intersection_name, test_intersection);        
        
        //incorrect test
        test_intersection = "Lawrence Avenue East & Yonge Street";//these streets do not intersect
        int_id = find_intersection_id_from_name(test_intersection);
        //cout << int_id << endl;
        
        //If streets do not intersect, find_intersection_id_from_name returns 0
        pos = getIntersectionPosition(int_id);     
        
        intersection_name = find_closest_intersection(pos);
        
        /*
         find_closest_intersection will return "Highway 401 Eastbound Collectors & Ramp to Highway 404 / Don Valley Parkway"
         because this is the intersection with intersection_id = 0
        */ 
        if(intersection_name == test_intersection)
        {
            CHECK(false);
            cout << "find_closest_intersection test" << endl;
        }
    }
    
    TEST(CONVERT_LATLON_TO_XY)
    {
        double expected = 5795.502445682106;//from m1_unittests.cpp
        LatLon latlon_point1(43.70674, -79.39832), latlon_point2(43.65572, -79.38369);//from m1_unittests.cpp
	
        t_point xy_point1, xy_point2;
        
        xy_point1 = latlon_to_xy(latlon_point1);
        xy_point2 = latlon_to_xy(latlon_point2);
        
        double dist_from_m2 = EARTH_RADIUS_IN_METERS * sqrt((xy_point2.y - xy_point1.y)*(xy_point2.y - xy_point1.y) + (xy_point2.x - xy_point1.x)*(xy_point2.x - xy_point1.x));
        CHECK(relative_error(expected, dist_from_m2) < 0.05);
    }
}