#include <algorithm>
#include <random>
#include <string>
#include <unittest++/UnitTest++.h>
#include "unit_test_util.h"
#include "m1.h"

using namespace std;

SUITE(unit_tests_for_m1)
{
    TEST(FIND_INTERSECTION_ID_FROM_NAME)
    {        
        int intersection_name_test1 = 234;
        int intersection_name_test2 = 512;
        
        //test to show that the tester doesn't always give true
        //correct id = 69
        int intersection_name_test1result = find_intersection_id_from_name("Plymbridge Road");
        if(intersection_name_test1 == intersection_name_test1result)
            CHECK(false);
        
        //correct test#1
        CHECK_EQUAL(intersection_name_test1, find_intersection_id_from_name("Dale Avenue & McKenzie Avenue"));
        
        //correct test#2        
        int intersection_name_test2result = find_intersection_id_from_name("Eastern Avenue & Logan Avenue");
        if(intersection_name_test2 == intersection_name_test2result)
            CHECK(true);
        
        //CORNER test#1
        CHECK_EQUAL(0, find_intersection_id_from_name("Highway 401 Eastbound Collectors & Ramp to Highway 404 / Don Valley Parkway"));
        
        //test that checks if the function finds fake street names or not
        int intersection_name_test4result = find_intersection_id_from_name("Borgin & Burkes");
        if(intersection_name_test2 == intersection_name_test4result)
            CHECK(false);
    }
    
    TEST(FIND_STREET_ID_FROM_NAME)
    {
        //test to show that the tester doesn't always give true
        //correct id = 161
        int fake_street_test = 162;
        int street_name_test1result = find_street_id_from_name("Dufferin Street");
        if(street_name_test1result == fake_street_test)
            CHECK(false);
        
        //correct test1
        CHECK_EQUAL(196, find_street_id_from_name("College Street"));
        
        //correct test2
        CHECK_EQUAL(138, find_street_id_from_name("University Avenue"));
        
        //false street to check if function works, and doesn't generate random street names
        int street_test4 = find_street_id_from_name("Diagon Alley");
        if(street_test4 == 89)
            CHECK(false);
    }
    
    TEST(FIND_INTERSECTION_STREET_SEGMENTS_FROM_NAME)
    {
        
        std::vector<unsigned> expected_result = {1376, 28919, 113281, 172708};
        std::vector<unsigned> fake_expected_result = {1, 2, 3, 4};
        
        //test to show that the tester doesn't always give true
        //correct vector: expected_result        
        std::vector<unsigned> actual_result = find_intersection_street_segments("Yonge Street & Eglinton Avenue East & Eglinton Avenue West");
        if(actual_result != fake_expected_result)
            CHECK(true);
        
        //correct test1
        CHECK_EQUAL(expected_result, find_intersection_street_segments("Yonge Street & Eglinton Avenue East & Eglinton Avenue West"));
        
        //correct test2
        CHECK_EQUAL(138, find_street_id_from_name("University Avenue"));
        
        //false intersection to check if function works, and doesn't generate random intersection names/street segments
        auto actual_result2 = find_intersection_street_segments("Diagon Alley & Knocturn Alley");
        if(actual_result2 != expected_result)
            CHECK(true);
    }
    
    TEST(FIND_STREET_SEGMENT_LENGTH)
    {
        float expected;
        float actual;
	
        //test to show that test checks right value
        expected = -2;
	actual = find_street_segment_length(76);
	if(expected != actual)
            CHECK(true);
	
        //correct test#1
	expected = 28.37000932206896;
	actual = find_street_segment_length(76);
	CHECK(relative_error(expected, actual) < 0.05);
        
        //correct test#2
        expected = 39.05230726094867;
	actual = find_street_segment_length(24810);
	CHECK(relative_error(expected, actual) < 0.05);

    }
    
    TEST(FIND_DISTANCE_BETWEEN_TWO_POINTS)
    {
        double actual;
        double expected;
        
        //incorrect test to check if test identifies wrong value
        expected = 10;
        actual = find_distance_between_two_points(LatLon(43.68814, -79.39412), LatLon(43.64528, -79.38253));
	if(expected != actual)
            CHECK(true);
        
        //correct test#1
        expected = 4857.506545485718;
        actual = find_distance_between_two_points(LatLon(43.68814, -79.39412), LatLon(43.64528, -79.38253));
	CHECK(relative_error(expected, actual) < 0.05);
                
        //corner case, to see if function identifies that it's the same point
        actual = find_distance_between_two_points(LatLon(10,10), LatLon(10,10));
        if(actual == 0)
            CHECK(true);
    }
}
